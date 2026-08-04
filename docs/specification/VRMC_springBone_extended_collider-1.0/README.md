# VRMC_springBone_extended_collider-1.0

*Version 1.0*

## Contributors

- 0b5vr

## Status

Complete

## Dependencies

glTF 2.0 사양을 향해 책정되었습니다.

본 사양은 [`VRMC_springBone`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_springBone-1.0/README.ja.md)에 의존합니다.

## Overview

`VRMC_springBone_extended_collider` 사양은, `VRMC_springBone`에서 정의된 콜라이더의 형태를 추가하는 glTF 확장입니다.
본 확장은 구 내부 콜라이더(Inside Sphere Collider), 캡슐 내부 콜라이더(Inside Capsule Collider), 평면 콜라이더(Plane Collider)를 정의합니다.

본 확장에 의해 추가되는 콜라이더는 `VRMC_springBone`에서 정의되는 콜라이더와 마찬가지로, 스프링본의 가동 범위를 충돌 판정에 의해 제한하기 위해 사용됩니다.

### Extended Colliders

본 확장에 의해 추가되는 콜라이더는 구 내부 콜라이더, 캡슐 내부 콜라이더, 평면 콜라이더 3종류입니다.

각 콜라이더에 대해, 참고 구현을 [Appendix: Reference Implementations](#appendix-reference-implementations)에 나타냅니다.

#### Inside Sphere Collider

일반적인 구 콜라이더가 스프링본을 구의 바깥쪽으로 제한하는 것에 반해, 구 내부 콜라이더는 스프링본을 구의 내부로 제한합니다.

구 내부 콜라이더는 일반적인 구 콜라이더와 마찬가지로 로컬 좌표계에서의 위치와 반지름으로 정의됩니다.

#### Inside Capsule Collider

일반적인 캡슐 콜라이더가 스프링본을 캡슐의 바깥쪽으로 제한하는 것에 반해, 캡슐 내부 콜라이더는 스프링본을 캡슐의 내부로 제한합니다.

캡슐 내부 콜라이더는 일반적인 캡슐 콜라이더와 마찬가지로 로컬 좌표계에서의 시작점 위치, 끝점 위치, 반지름으로 정의됩니다.

#### Plane Collider

평면 콜라이더는 스프링본을 평면의 한쪽 면으로 제한합니다.
평면은 무한 평면으로 정의되며 유한한 크기를 가지지 않습니다.

평면 콜라이더는 로컬 좌표계에서의 위치와 법선 벡터로 정의됩니다.

## glTF Schema Updates

### Extending Colliders

제약 조건(Constraint)은 `VRMC_springBone`에서 정의된 콜라이더에 `VRMC_springBone_extended_collider` 확장을 추가함으로써 기술됩니다.

```json
{
  "extensionsUsed": [
    "VRMC_springBone",
    "VRMC_springBone_extended_collider"
  ],
  "extensions": {
    "VRMC_springBone": {
      "specVersion": "1.0",
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 0.0,
              "offset": [0.0, -10000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0",
              "shape": {
                "sphere": {
                  "radius": 0.5,
                  "offset": [0.0, 0.0, 0.0],
                  "inside": true
                }
              }
            }
          }
        },
        // ...
      ]
    }
  },
  // 일반적인 glTF 2.0의 정보
  "nodes": [
    // ...
  ]
}
```

### Exporter Implemantation

> *이 섹션은 non-normative(비규범적)입니다.*

`VRMC_springBone_extended_collider` 확장으로 콜라이더가 정의되어 있는 경우, `VRMC_springBone_extended_collider`에 대응하지 않는 환경에서 적절하게 폴백(Fallback) 처리가 되도록, `VRMC_springBone`에서 정의된 콜라이더에는 무시되거나 근사되는 값을 출력할 것을 권장합니다.

#### Fallback: Inside Sphere Collider /  Inside Capsule Collider

내부 콜라이더가 되는 구・캡슐 콜라이더가 정의된 파일을 출력할 때, `VRMC_springBone_extended_collider`에 대응하지 않는 환경에서 폴백 콜라이더가 영향을 미치지 않도록, 위치를 원점에서 멀리 떨어진 구 콜라이더로 설정하는 등의 회피책을 검토해 주십시오.

아래에 폴백 콜라이더가 영향을 미치지 않도록 출력하는 경우의 예를 나타냅니다.

```json
    // 멀리 반지름 0의 구 콜라이더를 배치하여, 폴백 환경에서 유사하게 무시되도록 하는 예
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 0.0,
              "offset": [0.0, -10000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0-draft",
              "shape": {
                "sphere": {
                  "radius": 0.5,
                  "offset": [0.0, 0.0, 0.0],
                  "inside": true
                }
              }
            }
          }
        }
      ]
```

#### Fallback: Plane Collider

평면 콜라이더가 정의된 파일을 출력할 때, `VRMC_springBone_extended_collider`에 대응하지 않는 환경에서 폴백 콜라이더로도 평면 콜라이더와 유사한 동작을 실현할 수 있도록, 반경을 충분히 크게 한 구 콜라이더로 설정하는 등의 회피책을 검토해 주십시오.

아래에 폴백 콜라이더로 평면 콜라이더를 근사하도록 출력하는 경우의 예를 나타냅니다.

```json
    // 반지름 1000의 구 콜라이더를 배치하여, 폴백 환경에서 평면 콜라이더를 근사하는 예
    // float 형의 정밀도는 약 6자리. 0.1mm 정밀도라는 의미로 1000으로 설정했습니다.
      "colliders": [
        {
          "node": 0,
          "shape": {
            "sphere": {
              "radius": 1000.0,
              // plane의 offset - normal * radius를 지정해 주십시오
              "offset": [0.0, -1000.0, 0.0]
            }
          },
          "extensions": {
            "VRMC_springBone_extended_collider": {
              "specVersion": "1.0-draft",
              "shape": {
                "palne": {
                  "offset": [0.0, 0.0, 0.0],
                  "normal": [0.0, 1.0, 0.0],
                }
              }
            }
          }
        }
      ]
```

### VRMC_springBone_extended_collider

본 확장의 루트 오브젝트입니다.

#### Properties

||타입|설명|필수|
|:-|:-|:-|:-|
|`specVersion`|`string`|이 확장의 버전|✅ Yes|
|`shape`|[Shape](#shape)|콜라이더의 형태|No|

#### JSON Schema

[VRMC_springBone_extended_collider.schema.json](schema/VRMC_springBone_extended_collider.schema.json)

#### VRMC_springBone_extended_collider.specVersion ✅

`VRMC_springBone_extended_collider` 확장의 버전을 나타냅니다. 값은 `"1.0"`이어야 합니다.

- 타입: `string`
- 필수: Yes

#### VRMC_springBone_extended_collider.shape

콜라이더의 형태를 정의합니다.

- 타입: [Shape](#shape)
- 필수: No

### Shape

콜라이더의 형태를 정의합니다.

[ShapeSphere](#shapesphere), [ShapeCapsule](#shapecapsule), [ShapePlane](#shapeplane) 중 하나를 반드시 포함해야 합니다.

#### Properties

||타입|설명|필수|
|:-|:-|:-|:-|
|`sphere`|[ShapeSphere](#shapesphere)|구 콜라이더|No|
|`capsule`|[ShapeCapsule](#shapecapsule)|캡슐 콜라이더|No|
|`plane`|[ShapePlane](#shapeplane)|평면 콜라이더|No|

#### JSON Schema

[VRMC_springBone_extended_collider.shape.schema.json](schema/VRMC_springBone_extended_collider.shape.schema.json)

#### Shape.sphere

구 콜라이더를 정의합니다.

- 타입: [ShapeSphere](#shapesphere)
- 필수: No

#### Shape.capsule

캡슐 콜라이더를 정의합니다.

- 타입: [ShapeCapsule](#shapecapsule)
- 필수: No

#### Shape.plane

평면 콜라이더를 정의합니다.

- 타입: [ShapePlane](#shapeplane)
- 필수: No

### ShapeSphere

구 콜라이더의 형태를 정의합니다.

#### Properties

||타입|설명|필수|
|:-|:-|:-|:-|
|`offset`|`number[3]`|로컬 좌표계에서의 구의 중심 위치|No|
|`radius`|`number`|구의 반지름|No|
|`inside`|`boolean`|`true`인 경우 내부 콜라이더|No|

#### ShapeSphere.offset

로컬 좌표계에서의 구의 중심 위치를 나타냅니다.

- 타입: `number[3]`
- 필수: No, 초기값: [0.0, 0.0, 0.0]

#### ShapeSphere.radius

구의 반지름을 나타냅니다.

- 타입: `number`
- 필수: No, 초기값: 0.0
- 최소값: 0.0

#### ShapeSphere.inside

`true`인 경우 내부 콜라이더로 취급합니다.

- 타입: `boolean`
- 필수: No, 초기값: `false`

### ShapeCapsule

캡슐 콜라이더의 형태를 정의합니다.

#### Properties

||타입|설명|필수|
|:-|:-|:-|:-|
|`offset`|`number[3]`|로컬 좌표계에서의 캡슐 시작점 위치|No|
|`radius`|`number`|캡슐의 반지름|No|
|`tail`|`number[3]`|로컬 좌표계에서의 캡슐 끝점 위치|No|
|`inside`|`boolean`|`true`인 경우 내부 콜라이더|No|

#### ShapeCapsule.offset

로컬 좌표계에서의 캡슐 시작점 위치를 나타냅니다.

- 타입: `number[3]`
- 필수: No, 초기값: [0.0, 0.0, 0.0]

#### ShapeCapsule.radius

캡슐의 반지름을 나타냅니다.

- 타입: `number`
- 필수: No, 초기값: 0.0
- 최소값: 0.0

#### ShapeCapsule.tail

로컬 좌표계에서의 캡슐 끝점 위치를 나타냅니다.

- 타입: `number[3]`
- 필수: No, 초기값: [0.0, 0.0, 0.0]

#### ShapeCapsule.inside

`true`인 경우 내부 콜라이더로 취급합니다.

- 타입: `boolean`
- 필수: No, 초기값: `false`

### ShapePlane

평면 콜라이더의 형태를 정의합니다.

#### Properties

||타입|설명|필수|
|:-|:-|:-|:-|
|`offset`|`number[3]`|로컬 좌표계에서의 평면 위치|No|
|`normal`|`number[3]`|로컬 좌표계에서의 평면 법선 벡터|No|

#### ShapePlane.offset

로컬 좌표계에서의 평면 위치를 나타냅니다.

- 타입: `number[3]`
- 필수: No, 초기값: [0.0, 0.0, 0.0]

#### ShapePlane.normal

로컬 좌표계에서의 평면 법선 벡터를 나타냅니다.
정규화되어 있어야 합니다.

- 타입: `number[3]`
- 필수: No, 초기값: [0.0, 0.0, 1.0]

## Appendix: Reference Implementations

> 이하의 정보는 Non-normative(비규범적)입니다.

이하에 본 확장에서 정의하는 콜라이더의 참고 구현을 나타냅니다.

아래의 레퍼런스 구현에 의해 계산한 `distance`와 `direction`의 적용에 대해, `VRMC_springBone` 확장의 레퍼런스 구현도 함께 참조해 주십시오.

### Inside Sphere Collider

이하는 의사 코드(Pseudo-code)로 된 구 내부 콜라이더의 참고 구현입니다.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var delta = nextTail - transformedOffset;

// 조인트와 콜라이더 간의 거리. 음수 값은 충돌하고 있음을 나타냄
var distance = collider.radius - jointRadius - delta.magnitude;

// 조인트와 콜라이더 간의 거리의 방향. 충돌하고 있는 경우 이 방향으로 조인트를 밀어냄
var direction = -delta.normalized;
```

> Implementation Note: 내부 콜라이더가 아닌 일반 구 콜라이더와의 구현상 차이는 거리 계산 및 방향 계산 부분뿐입니다. 다른 부분에 대해서는 일반 구 콜라이더 구현을 유용할 수 있습니다.

### Inside Capsule Collider

이하는 의사 코드(Pseudo-code)로 된 캡슐 내부 콜라이더의 참고 구현입니다.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedTail = collider.tail * collider.worldMatrix;
var offsetToTail = transformedTail - transformedOffset;

var dot = dot(offsetToTail, delta);

var delta = nextTail - transformedOffset;

if (dot < 0.0) {
    // 조인트가 캡슐의 시작점 쪽에 있는 경우
    // 아무것도 하지 않음
} else if (dot > lengthSqCapsule) {
  // 조인트가 캡슐의 끝점 쪽에 있는 경우
    delta -= offsetToTail;
} else {
    // 조인트가 캡슐의 시작점과 끝점 사이에 있는 경우
    delta -= offsetToTail * (dot / offsetToTail.sqMagnitude);
}

// 조인트와 콜라이더 간의 거리. 음수 값은 충돌하고 있음을 나타냄
var distance = collider.radius - jointRadius - delta.magnitude;

// 조인트와 콜라이더 간의 거리의 방향. 충돌하고 있는 경우 이 방향으로 조인트를 밀어냄
var direction = -delta.normalized;
```

> Implementation Note: 내부 콜라이더가 아닌 일반 캡슐 콜라이더와의 구현상 차이는 거리 계산 및 방향 계산 부분뿐입니다. 다른 부분에 대해서는 일반 캡슐 콜라이더 구현을 유용할 수 있습니다.

### Plane Collider

이하는 의사 코드(Pseudo-code)로 된 평면 콜라이더의 참고 구현입니다.

```ts
var transformedOffset = collider.offset * collider.worldMatrix;
var transformedNormal = (colliderNormal * normalMatrixFrom(collider.worldMatrix)).normalized;
var delta = nextTail - transformedOffset;

// 조인트와 콜라이더 간의 거리. 음수 값은 충돌하고 있음을 나타냄
var distance = dot(delta, transformedNormal) - jointRadius;

// 조인트와 콜라이더 간의 거리의 방향. 충돌하고 있는 경우 이 방향으로 조인트를 밀어냄
var direction = transformedNormal;
```
