# VRMC_springBone

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

_Version 1.0_

## Contributors

- Shindo Tetsuro (進藤 哲郎)
- Su Po-Chang (蘇 柏彰)
- Obuchi Yutaka (小渕 豊)

## Status

Complete

## Dependencies

Written against the glTF 2.0 spec.

## Overview

Node에 관성에 의한 속도 유지와 원래 자세로 돌아가려는 스프링의 절차적 애니메이션(Procedural Animation)을 구현합니다.
머리카락이나 의상 등이 흔들리는 것과 같은 시각적인 용도를 가정하고 있습니다.
강성(stiffness), 감속(drag force), 중력(gravity) 등의 파라미터로 동작을 조정할 수 있습니다.
또한, SpringBone의 각 마디 끝(구)과 충돌 판정 Node(Collider: 구・캡슐)의 충돌을 설정할 수 있습니다.

## 구성

### 용어

설명을 위해 다음 용어를 도입합니다.

| 용어                                   | 의미                 | json                                             |
| -------------------------------------- | -------------------- | ------------------------------------------------ |
| SpringJoint                            | 설정이 된 node       | `springs[i].joints[j]`                           |
| HeadSpringJoint와 TailSpringJoint의 쌍 | 연속된 두 joint의 쌍 | `springs[i].joints[j]`, `springs[i].joints[j+1]` |
| SpringChain                            | 연속된 joint 전체    | `springs[i]`                                     |

#### SpringJoint

SpringBone 설정을 부여하는, 단일 glTF Node입니다.

#### HeadSpringJoint와 TailSpringJoint의 쌍

Head(`SpringJoint`)와 Tail(`SpringJoint`)의 쌍으로 표시되는 구간.
Tail에서 이전 프레임과의 위치 차이를 계산합니다.
설정이 있는 경우 Collider와의 충돌도 계산합니다.
반드시 Tail의 조상에 Head가 존재해야 하지만,
Head의 직접적인 부모가 아니어도 상관없습니다.

```
  흔들림
<- Tail ->
    |
    |
    o
   Head
```

Tail의 이동에서 Head의 회전을 산출합니다.

#### SpringChain

일련의 연속된 `SpringJoint`로 구성된 사슬 모양의 집합입니다.

```
a-b-c-d
```

### 예시

```
a-b-c-d
```

라는 joints가 있을 경우, 아래와 같이 해석합니다.

```
Head(회전함)
| +-Tail(이동 차이・충돌 판정)
v v
a-b
b-c
c-d
```

3개의 `HeadSpringJoint와 TailSpringJoint의 쌍`으로 전개됩니다.
끝부분의 SpringJoint는 Tail로만 사용되므로 SpringJoint의 파라미터는 사용되지 않습니다.
d를 회전시키고 싶은 경우는, 아래와 같이 끝에 Node를 추가하여 SpringJoint 설정을 하십시오.
추가하는 Node는 empty여도 상관없습니다.

```
Head(회전함)
| +-Tail(이동 차이・충돌 판정)
v v
a-b
b-c
c-d
d-e
```

### vrm0의 동작

```
a-b-c-d
```

라는 joints가 있을 경우, `vrm0`에서는 아래와 같이 해석합니다.

```
Head(회전함)
| +-Tail(이동 차이・충돌 판정)
v v
a-b
b-c
c-d
d-(끝부분 7cm 거리에 SpringJoint를 추가한다)
```

### 예외

#### 어떤 SpringJoint가 중복해서 여러 SpringChain에 소속되는 것(금지)

각각의 `SpringChain`은 동일한 joint를 포함해서는 안 됩니다.
아래의 경우, b, c, d가 동시에 2개의 SpringChain에 소속되어 있습니다.

```
b-c-d

a-b-c-d
```

아래와 같은 구성은 암묵적으로 중복된다고 간주합니다.
b, c가 동시에 2개의 SpringChain에 소속되어 있습니다.
생략(skip)되어 있어도 중복 판정의 대상이 됩니다.

```
()는 건너뛰어지는 node
a-(b)-c

b-(c)-e
```

#### 분기하는 SpringChain (미정의)

- 별개의 `SpringBoneChain`으로 취급해 주십시오.

```
  x-y-z
  |
a-b-c-d
```

- `a-b-c-d`와 `x-y-z` 2개의 SpringChain으로 처리한다.
- `a-b-c-d`와 `x-y-z` 중 어느 것을 먼저 처리할지, 이동 차이를 어느 타이밍에 얻을지 등은 특별히 지정하지 않고 미정의로 합니다. 구현에 따라 동작이 다를 수 있습니다. 병렬 실행 등 구현의 편의를 우선해도 좋습니다.

## 평가하는 좌표계

SpringBone에서 Joint 위치 평가에는 원칙적으로 World Space를 사용합니다.

### Center Space

`center` 프로퍼티를 이용함으로써 Joint 위치 평가에 World Space 이외의 것을 사용할 수 있습니다.
`center`는 모델 내의 한 Node를 지정할 수 있으며, 지정한 Node로부터 상대적인 Space에서 Joint 위치가 평가됩니다.
`center`는 SpringChain 단위로 지정합니다.

Center 노드는 해당 SpringChain의 0번째 Joint이거나 그 조상 node여야 합니다.
또한 Center 노드에는 다른 SpringChain의 Joint 노드 및 그 자손을 지정할 수 없습니다.

다음과 같이, 주로 SpringBone이 너무 많이 흔들리는 경우에 유효합니다.

- 모델이 보행 등으로 평행 이동했을 때, SpringBone이 너무 많이 흔들리는 경우
- 머리에 붙어 있는 SpringBone (예: 머리카락이나 머리 장식)에 대해 머리를 움직였을 때만 반응하여 움직이기를 원하는 경우

## JSON

```json
{
  "extensionsUsed": ["VRMC_springBone"],
  "extensions": {
    "VRMC_springBone": {
      // collider 배열
      "specVersion": "1.0",
      "colliders": [
        {
          "node": 2,
          "shape": {
            "sphere": {
              "offset": [0, 0, 0],
              "radius": 1
            }
          }
        },
        {
          "node": 2,
          "shape": {
            "capsule": {
              "offset": [0, 0, 0],
              "radius": 1,
              "tail": [0, 0, 1]
            }
          }
        }
      ],
      // colliderGroup 배열
      "colliderGroups": [
        {
          // group0
          "name": "group0",
          "colliders": [0, 1]
        }
      ],
      // springBone 배열
      "springs": [
        {
          "joints": [
            {
              "node": 0 // node0
            },
            {
              "node": 1 // node1
            }
          ],
          "colliderGroups": [0]
        }
      ]
    }
  },
  // 일반적인 GLTF-2.0 정보
  "nodes": [
    {
      "name": "node0"
    }
    // 생략
  ]
}
```

### `VRMC_springBone.specVersion`

VRMC_springBone 확장의 사양 버전을 나타냅니다.

```json
extensions.VRMC_springBone.specVersion = "1.0"
```

### `VRMC_springBone.colliders`

SpringBone에 대한 충돌 판정을 정의합니다.
대상 Node와 그 모양(shape)입니다.

```json
{
  "extensions": {
    "VRMC_springBone": {
      "colliders": [
        {
          "node": 1,
          "shape": {
            "sphere": {
              "offset": [0, 0, 0],
              "radius": 1
            }
          }
        },
        {
          "node": 1,
          "shape": {
            "capsule": {
              "offset": [0, 0, 0],
              "radius": 1,
              "tail": [0, 0, 1]
            }
          }
        }
      ]
    }
  }
}
```

shape는 `sphere` 또는 `capsule` 중 하나로 배타적입니다.

| key                  | type    | 비고                                                                                            |
| :------------------- | :------ | :---------------------------------------------------------------------------------------------- |
| node                 | integer | 대상 노드                                                                                       |
| shape.sphere.offset  | float3  | shape가 구(sphere)인 경우에만: 대상 노드의 로컬 좌표계에서의 구의 중심 위치                     |
| shape.sphere.radius  | float   | shape가 구(sphere)인 경우에만: 구의 반지름                                                      |
| shape.capsule.offset | float3  | shape가 캡슐(capsule)인 경우에만: 대상 노드의 로컬 좌표계에서의 캡슐 시작점 쪽 반원의 중심 위치 |
| shape.capsule.radius | float   | shape가 캡슐(capsule)인 경우에만: 캡슐의 반원 부분과 원기둥 부분의 반지름                       |
| shape.capsule.tail   | float3  | shape가 캡슐(capsule)인 경우에만: 대상 노드의 로컬 좌표계에서의 캡슐 끝점 쪽 반원의 중심 위치   |

### `VRMC_springBone.colliderGroups`

```json
{
  "extensions": {
    "VRMC_springBone": {
      "colliderGroups": [
        {
          "name": "groupName",
          "colliders": [0, 1, 2]
        }
      ]
    }
  }
}
```

| key       | type      | 비고                                                  |
| :-------- | :-------- | :---------------------------------------------------- |
| name      | string    | 그룹의 이름                                           |
| colliders | integer[] | 앞 항목의 VRMC_springBone.colliders에 대한 index 목록 |

### `VRMC_springBone.springs`

```json
{
  "extensions": {
    "VRMC_springBone": {
      "springs": [
        {
          "name": "spring0",
          "joints": [
            // 다음 항목을 참조하십시오
          ],
          "colliderGroups": [0],
          "center": 0
        }
      ]
    }
  }
}
```

| 이름           | 비고                                                               |
| :------------- | :----------------------------------------------------------------- |
| name           | Spring 이름                                                        |
| joints         | springBone을 구성하는 Joint 목록                                   |
| colliderGroups | 이 spring과 충돌하는 `VRMC_springBone.colliderGroups`의 index 목록 |
| center         | [Center Space](#center-space)의 루트로 사용할 노드의 인덱스        |

#### joints

`SpringBoneChain`을 나타냅니다.
다음 제약이 있습니다.

- joints[n]은 joints[n+1]의 부모 또는 조상일 것

joints[n]과 joints[n+1]이 직접적인 부모 자식 node가 아닐 경우, 사이의 node는 무시됩니다.
joints의 마지막이 끝 node가 아닐 경우, 그 자손 node는 무시되어 개별적으로 흔들리지 않습니다.

> 위의 설명대로, joints를 설정하지 않음으로써 중간 혹은 끝부분의 node를 건너뛰고 흔들리도록 설정할 수 있습니다. 그러나 해당 node에 다른 용도가 없는 경우 그 node는 불필요하게 남아있는 것이므로 노드 자체를 삭제할 것을 권장합니다.

### `VRMC_springBone.springs[*].joints[*]`

```json
{
  "extensions": {
    "VRMC_springBone": {
      "springs": [
        {
          "joints": [
            {
              "node": 0,
              "hitRadius": 0.1,
              "stiffness": 0.5,
              "gravityPower": 1.0,
              "gravityDir": [0, -1, 0],
              "dragForce": 0.5
            },
            {
              "node": 1
              // 끝부분 joint는 node 이외에는 필요하지 않습니다.
            }
          ]
        }
      ]
    }
  }
}
```

| 이름         | 값           | 비고                                           |
| :----------- | :----------- | :--------------------------------------------- |
| node         | integer      | 대상 node의 index                              |
| hitRadius    | float(meter) | springBone의 충돌 판정 크기                    |
| stiffness    | 0 이상       | 강성 (초기 상태로 돌아가려는 힘)               |
| gravityPower |              | 중력의 힘 (SpringBone에 매 프레임 가해지는 힘) |
| gravityDir   | [x, y, z]    | 중력 방향                                      |
| dragForce    | [0-1]        | 감속 (SpringBone을 감속시키는 힘)              |

## SpringBone의 알고리즘

> _이 섹션은 non-normative(비규범적)입니다._

이 섹션에서는 SpringBone의 레퍼런스 구현을 보여줍니다.

실제 구현은 UniVRM이나 three-vrm 등, VRM 각 구현의 소스 코드에서 볼 수 있습니다.

SpringBone의 레퍼런스 구현은 Verlet 적분(Verlet Integration)을 사용하여 간단한 물리 시뮬레이션을 수행하는 것입니다.

### 업데이트 순서

SpringBone 계통 전체의 업데이트는 SpringBoneJoint끼리의 의존성을 해결하며 이루어집니다.
구체적으로, Joint의 부모에 Joint가 존재할 경우 그 부모가 우선적으로 처리됩니다.
즉, 근본(루트)에서부터 끝부분(팁)을 향해 순서대로 업데이트 처리가 이루어집니다.

### 초기화

본 구현에서 다루는 하나의 SpringBoneJoint는 아래와 같은 상태(State)를 가집니다:

```ts
interface SpringBoneJointState {
  prevTail: Vector3;
  currentTail: Vector3;
  boneAxis: Vector3;
  boneLength: number;
  initialLocalMatrix: Matrix4;
  initialLocalRotation: Quaternion;
}
```

`prevTail` ・ `currentTail`은 그 Joint가 대상으로 삼는 자식 Node의 월드 공간 내 위치를 나타냅니다.
`currentTail`이 현재 프레임의 위치, `prevTail`이 이전 프레임의 위치를 나타냅니다.

`boneAxis`는 그 Joint가 대상으로 삼는 자식 Node의 로컬 공간 내 정지(Rest) 상태에서 뻗어나가는 방향을 나타냅니다.
`boneLength`는 그 Joint가 대상으로 삼는 자식 Node의 월드 공간 내 길이를 나타냅니다.

`initialLocalMatrix`는 그 Joint가 대상으로 삼는 Node의 정지(Rest) 상태에서의 트랜스폼을 나타냅니다.
`initialLocalRotation`은 그 Joint가 대상으로 삼는 Node의 정지(Rest) 상태에서의 회전을 나타냅니다.

### 업데이트 처리

대략적으로, 아래의 3단계로 업데이트가 이루어집니다:

- 관성 계산
- Collider와의 충돌
- 회전에 대한 반영

#### 관성 계산

아래의 3가지 힘을 계산하여 `currentTail`의 위치를 업데이트합니다.

- 관성: tail이 관성에 의해 움직이려는 힘. `1.0 - dragForce`가 계수가 됩니다.
- 강성: tail이 원래 방향으로 돌아가려는 힘. `stiffnessForce`가 계수가 됩니다.
- 중력: tail이 중력에 의해 끌려가는 힘. `gravityDir * gravityPower`가 계수가 됩니다.

아래 의사 코드(Pseudo-code)로 처리를 나타냅니다:

```ts
var {currentTail, prevTail, initialLocalRotation, boneAxis, boneLength} = state;
var {dragForce, stiffnessForce, gravityDir, gravityPower} = props;

var worldPosition = node.worldPosition;
var parentWorldRotation = node.parent ? node.parent.worldRotation : Quaternion.identity;

// verlet 적분으로 다음 위치 계산
var inertia = (currentTail - prevTail) * (1.0f - dragForce);
var stiffness = deltaTime * parentWorldRotation * initialLocalRotation * boneAxis * stiffnessForce;
var external = deltaTime * gravityDir * gravityPower;

var nextTail = currentTail + inertia + stiffness + external;

// 길이 제약
nextTail = worldPosition + (nextTail - worldPosition).normalized * boneLength;
```

#### Collider와의 충돌

Joint가 대상으로 하는 Collider와의 충돌 판정을 수행합니다.
Collider 각각에 대해 거리 판정을 수행하며, 거리가 Collider와 Joint의 반지름 합보다 작을 경우 그것들이 접하는 위치까지 조인트를 밀어내는 처리를 합니다.

아래 의사 코드(Pseudo-code)로 처리를 나타냅니다:

```ts
for (var collider of colliders) {
  var { direction, distance } = collider.calculateCollision(nextTail);

  if (distance < 0.0) {
    // 밀어낸다
    nextTail = nextTail - direction * distance;

    // 길이 제약
    nextTail =
      worldPosition + (nextTail - worldPosition).normalized * boneLength;
  }
}
```

#### 구 콜라이더 (Sphere Collider)

아래 의사 코드(Pseudo-code)에 구 콜라이더의 참고 구현을 나타냅니다.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var delta = nextTail - transformedOffset;

// 조인트와 콜라이더 간의 거리. 음수 값은 충돌하고 있음을 나타냄
var distance = delta.magnitude - collider.radius - jointRadius;

// 조인트와 콜라이더 간의 거리의 방향. 충돌하고 있는 경우 이 방향으로 조인트를 밀어냄
var direction = delta.normalized;
```

#### 캡슐 콜라이더 (Capsule Collider)

아래 의사 코드(Pseudo-code)에 캡슐 콜라이더의 참고 구현을 나타냅니다.

```ts
let transformedOffset = collider.offset * collider.worldMatrix;
let transformedTail = collider.tail * collider.worldMatrix;
let offsetToTail = transformedTail - transformedOffset;

let dot = dot(offsetToTail, delta);

var delta = nextTail - transformedOffset;

if (dot < 0.0) {
  // 조인트가 캡슐의 시작점 쪽에 있는 경우
  // 아무것도 하지 않음
} else if (dot > offsetToTail.sqMagnitude) {
  // 조인트가 캡슐의 끝점 쪽에 있는 경우
  delta -= offsetToTail;
} else {
  // 조인트가 캡슐의 시작점과 끝점 사이에 있는 경우
  delta -= offsetToTail * (dot / offsetToTail.sqMagnitude);
}

// 조인트와 콜라이더 간의 거리. 음수 값은 충돌하고 있음을 나타냄
let distance = delta.magnitude - collider.radius - jointRadius;

// 조인트와 콜라이더 간의 거리의 방향. 충돌하고 있는 경우 이 방향으로 조인트를 밀어냄
let direction = delta.normalized;
```

#### 회전에 대한 반영

위 계산에서 얻은 새로운 `nextTail`을 바탕으로, Joint가 대상으로 하는 Node의 회전을 업데이트합니다.

아래 의사 코드(Pseudo-code)로 처리를 나타냅니다:

```ts
// prevTail・currentTail 업데이트
prevTail = currentTail;
currentTail = nextTail;

// 회전 업데이트
var to = (nextTail * (node.parent.worldMatrix * initialLocalMatrix).inverse)
  .normalized;
node.rotation = initialLocalRotation * fromToQuaternion(boneAxis, to);
```

#### Center space의 고려

SpringBone에 `center`가 설정된 경우, SpringBone의 관성은 [Center Space](#center-space)에서 평가됩니다.
이는 위의 관성 계산에서 World Space에서 평가하던 트랜스폼을 대신하여 Center Space에서 평가함으로써 구현할 수 있습니다.
외력(중력)에 대해서는 `center` 설정과 무관하게 World Space에서 계산됩니다.
