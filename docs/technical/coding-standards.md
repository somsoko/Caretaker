# 코딩 컨벤션 (Coding Standards)

**프로젝트**: Caretaker  
**언어**: C# 12 / Unity 6

---

## 목차

1. [네이밍 규칙](#1-네이밍-규칙)
2. [파일 구조](#2-파일-구조)
3. [클래스 & 컴포넌트](#3-클래스--컴포넌트)
4. [Unity 특화 규칙](#4-unity-특화-규칙)
5. [주석 & 문서화](#5-주석--문서화)
6. [금지 패턴](#6-금지-패턴)

---

## 1. 네이밍 규칙

| 대상 | 컨벤션 | 예시 |
|------|--------|------|
| 클래스 / 인터페이스 | `PascalCase` | `PlayerController`, `IDamageable` |
| 인터페이스 | `I` 접두사 + `PascalCase` | `IInteractable` |
| public 메서드 | `PascalCase` | `TakeDamage()` |
| private 메서드 | `PascalCase` | `HandleInput()` |
| private 필드 | `_camelCase` (언더스코어) | `_currentHealth` |
| `[SerializeField]` 필드 | `_camelCase` | `_moveSpeed` |
| public 프로퍼티 | `PascalCase` | `CurrentHealth { get; private set; }` |
| 로컬 변수 | `camelCase` | `hitPoint` |
| 상수 / static readonly | `UPPER_SNAKE_CASE` | `MAX_HEALTH` |
| 이벤트 | `On` + `PascalCase` | `OnHealthChanged` |
| Enum | `PascalCase` | `GameState`, `GameState.Playing` |
| ScriptableObject 에셋 | `SO_` + 설명 | `SO_PlayerStats` |

---

## 2. 파일 구조

### 2.1 파일당 하나의 public 클래스

```csharp
// ✅ Good — PlayerController.cs 파일에 PlayerController만
public class PlayerController : MonoBehaviour { }

// ❌ Bad — 여러 public 클래스를 한 파일에
public class PlayerController : MonoBehaviour { }
public class PlayerAnimator : MonoBehaviour { }
```

### 2.2 using 정렬 순서

```csharp
// 1. System 네임스페이스
using System;
using System.Collections.Generic;

// 2. Unity 네임스페이스
using UnityEngine;
using UnityEngine.InputSystem;

// 3. 서드파티
using Unity.Netcode;

// 4. 프로젝트 내부 (네임스페이스 확정 후 갱신)
// using Caretaker.Core;
// using Caretaker.Gameplay;
```

---

## 3. 클래스 & 컴포넌트

### 3.1 MonoBehaviour 멤버 순서

```csharp
public class PlayerController : MonoBehaviour
{
    // 1. 상수
    private const float MOVE_SPEED_MULTIPLIER = 1.5f;

    // 2. Serialize 필드 (Inspector 노출)
    [Header("Movement")]
    [SerializeField] private float _moveSpeed = 5f;
    [SerializeField] private float _jumpForce = 10f;

    // 3. private 필드
    private Rigidbody _rb;
    private bool _isGrounded;

    // 4. 프로퍼티
    public bool IsGrounded => _isGrounded;

    // 5. 이벤트
    public event Action<float> OnHealthChanged;

    // 6. Unity 생명주기 (Awake → OnEnable → Start → Update → ... → OnDestroy)
    private void Awake() { }
    private void OnEnable() { }
    private void Start() { }
    private void Update() { }
    private void FixedUpdate() { }
    private void OnDisable() { }
    private void OnDestroy() { }

    // 7. public 메서드
    public void TakeDamage(float amount) { }

    // 8. private 메서드
    private void HandleMovement() { }

    // 9. 코루틴
    private IEnumerator SpawnRoutine() { }
}
```

### 3.2 인터페이스 활용

```csharp
// 다형성이 필요한 상호작용에는 인터페이스 사용
public interface IDamageable
{
    void TakeDamage(float amount);
    float MaxHealth { get; }
}

public interface IInteractable
{
    void Interact(PlayerController player);
    string InteractionPrompt { get; }
}
```

---

## 4. Unity 특화 규칙

### 4.1 GetComponent 캐싱 필수

```csharp
// ✅ Good
private Rigidbody _rb;
private void Awake() => _rb = GetComponent<Rigidbody>();

// ❌ Bad — Update에서 호출
private void Update() => GetComponent<Rigidbody>().AddForce(Vector3.up);
```

### 4.2 SerializeField 사용 (public 필드 금지)

```csharp
// ✅ Good
[SerializeField] private float _speed = 5f;

// ❌ Bad
public float speed = 5f;
```

### 4.3 null 체크 — Unity 오퍼레이터 주의

```csharp
// ✅ Unity != null 연산자 사용 (UnityEngine.Object 전용)
if (target != null) { }

// ✅ C# is null 패턴 — Destroy된 오브젝트 감지 못함에 주의
if (target is not null) { }

// ❌ ?. null-conditional은 UnityEngine.Object에서 예상과 다르게 동작할 수 있음
// target?.DoSomething();  ← 주의해서 사용
```

### 4.4 Coroutine vs Async/Await

```csharp
// Unity 6에서 awaitable API 사용 가능
// 씬 로딩 등 Unity 내부 비동기 작업: Coroutine 또는 Awaitable
public async Awaitable LoadSceneAsync(string sceneName)
{
    await SceneManager.LoadSceneAsync(sceneName);
}

// 순수 C# 비동기 (네트워크, IO): Task/ValueTask
public async Task<string> FetchDataAsync() { }
```

### 4.5 ScriptableObject 데이터 패턴

```csharp
// 밸런스 데이터는 ScriptableObject로 분리
[CreateAssetMenu(fileName = "SO_PlayerStats", menuName = "Caretaker/Stats/Player")]
public class PlayerStatsSO : ScriptableObject
{
    [field: SerializeField] public float MaxHealth { get; private set; } = 100f;
    [field: SerializeField] public float MoveSpeed { get; private set; } = 5f;
}
```

### 4.6 이벤트 구독 해제 필수

```csharp
private void OnEnable()  => GameManager.Instance.OnGameStateChanged += HandleStateChange;
private void OnDisable() => GameManager.Instance.OnGameStateChanged -= HandleStateChange;
```

---

## 5. 주석 & 문서화

### 5.1 XML 문서 주석 — public API 필수

```csharp
/// <summary>
/// 플레이어에게 데미지를 적용합니다.
/// </summary>
/// <param name="amount">적용할 데미지량 (양수)</param>
/// <returns>처리 후 남은 체력</returns>
public float TakeDamage(float amount) { }
```

### 5.2 인라인 주석 — 비즈니스 로직 설명

```csharp
// ✅ Good — '왜'를 설명
// 프레임레이트에 독립적인 감속을 위해 Time.deltaTime 대신 fixedDeltaTime 사용
_velocity *= Mathf.Pow(1f - _drag, Time.fixedDeltaTime);

// ❌ Bad — '무엇'을 그대로 반복
// velocity에 drag를 곱함
_velocity *= drag;
```

### 5.3 TODO 형식

```csharp
// TODO [LIN-42]: 시간 되감기 버퍼 최적화 필요 — 현재 메모리 과다 사용
// FIXME [LIN-61]: 고스트 동기화 시 위치 보간 끊김 현상
```

---

## 6. 금지 패턴

| 패턴 | 이유 | 대안 |
|------|------|------|
| `FindObjectOfType<T>()` 빈번 사용 | O(n) 씬 탐색, 성능 저하 | Singleton, 이벤트, Inspector 참조 |
| `SendMessage` / `BroadcastMessage` | 타입 안전성 없음, 느림 | 인터페이스, 이벤트 |
| 하드코딩 문자열 태그 | 오타 컴파일 오류 미감지 | `static class Tags { const string Player = "Player"; }` |
| `Update()` 내 `new` 할당 | GC 압박 | 풀링, 사전 할당 |
| `Camera.main` 반복 호출 | 내부적으로 FindObjectOfType | Awake에서 캐싱 |
| `public` 필드 | Inspector 노출 + 캡슐화 파괴 동시 | `[SerializeField] private` |
