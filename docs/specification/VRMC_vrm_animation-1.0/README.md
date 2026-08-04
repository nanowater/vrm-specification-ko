# VRMC_vrm_animation

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

_Version 1.0_

## Contents

## Contributors

- 신도 테츠로 (進藤 哲郎)
- 0b5vr

## Status

Complete

## Dependencies

glTF 2.0 사양을 향해 책정되었습니다.

또한, 사양 내 일부 정의가 [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.ja.md)에 의존합니다.

## Overview

`VRMC_vrm_animation` 사양은 인간형 모델에 대한 애니메이션을 기술하기 위한 glTF 확장입니다.
인간형 모델을 기술하는 glTF 확장인 [`VRMC_vrm`](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/README.ja.md)으로 표현된 모델에 적용하는 것을 가정하고 있습니다.

glTF에서 정의된 노드의 계층 구조에 대해, 각 노드와 인간의 본(bone), 표정, 시선 방향을 연관지어, 임의의 인간형 모델에 적용할 수 있는 애니메이션을 표현합니다.
애니메이션의 실제 데이터는 glTF의 코어 정의의 애니메이션 부분을 이용합니다.

애니메이션만을 기술하는 독립적인 glTF 파일에 사용되는 것을 가정하고 있습니다.
`VRMC_vrm`을 사용하여 기술된 VRM 모델에 본 확장이 포함되는 것은 가정하고 있지 않습니다.

## Concepts

### Animations

glTF의 코어 정의의 애니메이션을 이용합니다.

원칙적으로 애니메이션을 사용할 때는 `animations`에 정의된 첫 번째 애니메이션을 불러오는 것으로 합니다.
하나의 파일에 여러 애니메이션이 포함되어 있을 수 있습니다.
구현은 하나의 파일 내에 포함된 여러 애니메이션을 지원할 수 있지만, 필수는 아닙니다.

glTF 애니메이션은 임의 시간에 키프레임을 삽입할 수 있으므로 프레임 레이트(frame rate)의 개념이 없습니다.
다음 사항을 고려하여 너무 높지도 낮지도 않은 프레임 레이트의 기준으로 초당 30프레임을 제안합니다.

- 게임에서는 60 FPS나 30 FPS가 일반적입니다
- 모션 캡처의 원시 데이터(가공 전)는 높은 프레임 레이트인 경우가 있습니다
- 영상에서는 24FPS 등이 있습니다

애니메이션을 Linear로 Interpolation(보간)하는 것을 가정하여,
30 FPS로 충분한 부드러움을 확보할 수 있을 것으로 보입니다.

### Humanoid

Humanoid는 본 확장 내에서 정의하는 인간 본의 정의입니다.
각 Humanoid 본은 glTF의 노드로 표현됩니다.

`VRMC_vrm_animation/humanoid` 내에서 glTF의 노드와 인간의 본의 대응 관계를 나타냅니다.
Humanoid에서 이용하는 본 및 그 구조에 대해서는 `VRMC_vrm` 확장의 Humanoid를 따릅니다.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.ja.md

단, Humanoid 본 중 `leftEye`와 `rightEye`는 이에 대해 애니메이션 데이터를 가질 수 없습니다.
이들은 후술할 LookAt에서 다뤄집니다.

> 미래의 확장성을 고려하여, Humanoid 본의 계층 구조 전체를 애니메이션 데이터 내에 수록하고 있습니다.

Humanoid 본으로 지정된 노드는 애니메이션이 적용되지 않은 레스트 포즈(rest pose)에서 "VRM T-pose"라고 불리는 포즈를 따라야 합니다.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md

Humanoid 본으로 지정된 노드에 대한 애니메이션을 애니메이션 데이터로 취급합니다.
Humanoid 본에 대한 애니메이션에 스케일(scale)을 포함해서는 안 됩니다.
또한 Hips 본 이외의 애니메이션에 평행 이동(translation)을 포함해서는 안 됩니다.

> 구현 내에서는, 예를 들어 Unity의 Humanoid 시스템을 사용하여 고급 애니메이션 전송이 이루어지는 것이 바람직하지만, 이것이 어려울 경우 단순히 FK 애니메이션으로 전송할 수도 있습니다.

또한 애니메이션 데이터와 별개로 `T-Pose`를 표현하는 node 계층 구조에 `scale`을 포함하지 않는 것을 강력히 권장합니다.

> 데이터를 생성하는 애플리케이션에서 계층 구조와 애니메이션에 스케일 값을 사전에 곱함으로써 해결할 수 있습니다.
> 런타임(runtime)에 로드하는 프로그램 측의 부담을 줄이는 것을 의도하고 있습니다.

반면에 회전은 임의의 값이라도 문제 없습니다. [포즈 데이터의 호환성에 대하여](./how_to_transform_human_pose.md)를 참조하십시오.

### Expressions

Expressions는 본 확장 내에서 정의하는 인간 표정의 정의입니다.
각 Expressions는 [0, 1] 범위의 스칼라(scalar)를 가중치(weight)로 가지며, 이 수치가 그 표정이 어느 정도 발현되었는지를 나타냅니다.

> `VRMC_vrm` 확장에서는 Expressions의 가중치가 메시의 모프(morph), 매터리얼의 색상, 텍스처의 UV에 적용됩니다.

`VRMC_vrm_animation/expressions` 내에서 glTF의 노드와 표정과의 대응 관계를 나타냅니다.
Expressions의 정의에 대해서는 `VRMC_vrm` 확장의 Expressions를 따릅니다.

https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/expressions.ja.md

단, 프리셋 표정 중 `lookUp`, `lookDown`, `lookLeft`, `lookRight`는 이에 대해 애니메이션 데이터를 가질 수 없습니다.
이들은 후술할 LookAt에서 다뤄집니다.

Expressions 중 `VRMC_vrm` 확장에서 프리셋 표정으로 정의된 것을 프리셋 표정(preset expression), 그 이외를 커스텀 표정(custom expression)으로 정의합니다.
커스텀 표정에 프리셋과 같은 이름의 표정을 포함할 수 없습니다.

Expressions로 지정된 노드에 대한 애니메이션 중, 평행 이동의 X 성분을 해당 표정의 가중치에 대한 애니메이션 데이터로 취급합니다.
가중치 값은 [0, 1] 범위 내에 들어가는 것이 바람직합니다.
구현은 [0, 1] 범위를 벗어난 가중치 값을 다룰 때, 그 값을 [0, 1] 범위 내로 맞추어 다루어야 합니다.

### LookAt

LookAt은 본 확장 내에서 정의하는 인간의 시선에 대한 정의입니다.

> `VRMC_vrm` 확장에서 LookAt에 의한 시선 제어는 Humanoid의 본 회전에 의한 애니메이션과 Expressions에 의한 애니메이션 2가지 동작 방식을 지원합니다.

#### 시선 방향

LookAt은 하나의 시선 방향을 가지며, 모델이 그 방향으로 시선을 움직이는 것을 나타냅니다.

`VRMC_vrm_animation/lookAt/node`에서 시선 방향을 회전(rotation)으로 가지는 glTF의 노드를 지정합니다.
지정한 노드의 로컬 공간에서의 회전을 시선 방향의 애니메이션 데이터로 취급합니다.

glTF에서는 회전이 쿼터니언(quaternion)으로 정의되지만, `VRMC_vrm`의 LookAt 컴포넌트에 적용할 때는 yaw-pitch의 오일러 각(Euler angles)으로 변환하여 다룹니다.
이때 오일러 각의 회전 순서는 Extrinsic ZXY로 해석하고, Y축 주위의 회전을 yaw, X축 주위의 회전을 pitch로 합니다.

#### 시점 위치

본 확장 내에서 정의되는 LookAt에 있어서 시점 위치는, [`VRMC_vrm` 사양 내에서 정의된 LookAt 공간](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/lookAt.ja.md#lookat-공간-offsetfromheadbone)을 따라, Humanoid에서 정의된 head 본의 트랜스폼으로부터의 상대적인 공간에 있어서, `VRMC_vrm_animation/offsetFromHeadBone`만큼 평행 이동한 위치로 합니다.
또한 그 시선은 초기 상태에서 모델 좌표계의 +Z방향을 향하고 있는 것으로 정의합니다.

> Implementation Note: 시선 방향을 가공하지 않고 그대로 이용할 경우, `offsetFromHeadBone`의 값을 해석할 필요는 없습니다. 전송 대상 모델의 체격에 맞춰 시점 정보에 어떤 형태의 리타겟팅(retargeting)을 적용하고 싶은 경우, `offsetFromHeadBone`의 값을 더한 애니메이션 측 모델의 시점 위치를 활용하는 것이 효과적일 수 있습니다.

Humanoid가 정의되지 않은 경우 `offsetFromHeadBone`의 값을 그대로 시점 위치로 해석합니다.

## glTF Schema Updates

`VRMC_vrm_animation` 확장은 glTF의 루트 수준에서 정의됩니다.

```json
{
  "nodes": [
    // ...
  ],
  "animations": [
    // ...
  ],
  "extensions": {
    "VRMC_vrm_animation": {
      "specVersion": "1.0",
      "humanoid": {
        "humanBones": {
          "hips": { "node": 0 },
          "spine": { "node": 1 },
          "chest": { "node": 2 }
          // ...
        }
      },
      "expressions": {
        "preset": {
          "aa": { "node": 59 },
          "blinkLeft": { "node": 60 },
          "blink": { "node": 61 },
          "happy": { "node": 62 },
          "relaxed": { "node": 63 }
        }
      },
      "lookAt": {
        "node": 64,
        "offsetFromHeadBone": [0.0, 0.06, 0.0]
      }
    }
  }
  // ...
}
```

### VRMC_vrm_animation

본 확장의 루트 객체(root object)입니다.

#### Properties

|               | 타입          | 설명                                | 필수   |
| :------------ | :------------ | :---------------------------------- | :----- |
| `specVersion` | `string`      | 본 확장의 사양 버전                 | ✅ Yes |
| `humanoid`    | `humanoid`    | Humanoid 본에 관한 정의             | No     |
| `expressions` | `expressions` | Expressions 표정과 노드의 대응 관계 | No     |
| `lookAt`      | `lookAt`      | 시점·시선에 관한 정의               | No     |

#### JSON Schema

[VRMC_vrm_animation.schema.json](schema/VRMC_vrm_animation.schema.json)

#### VRMC_vrm_animation.specVersion ✅

`VRMC_vrm_animation` 확장의 사양 버전을 나타냅니다. 값은 `"1.0"`입니다.

- 타입: `string`
- 필수: Yes

#### VRMC_vrm_animation.humanoid

Humanoid 본과 노드의 대응 관계를 나타내는 객체입니다.

- 타입: `humanoid`
- 필수: No

#### VRMC_vrm_animation.expressions

Expressions 표정과 노드의 대응 관계를 나타내는 객체입니다.

- 타입: `expressions`
- 필수: No

#### VRMC_vrm_animation.lookAt

시점·시선에 관한 정의를 수행하는 객체입니다.

- 타입: `lookAt`
- 필수: No

---

### humanoid

Humanoid 본에 관한 정의를 수행하는 객체입니다.

#### Properties

|            | 타입                  | 설명                                    | 필수   |
| :--------- | :-------------------- | :-------------------------------------- | :----- |
| humanBones | `humanoid.humanBones` | Humanoid 본과 노드의 대응 관계를 나타냄 | Yes ✅ |

#### JSON Schema

[VRMC_vrm_animation.humanoid.schema.json](schema/VRMC_vrm_animation.humanoid.schema.json)

#### humanoid.humanBones ✅

Humanoid 본과 노드의 대응 관계를 나타내는 객체입니다.

- 타입: `humanoid.humanBones`
- 필수: Yes

### humanoid.humanBones

Humanoid 본과 노드의 대응 관계를 나타내는 객체입니다.

#### Properties

|                    | 타입                            | 설명               | 필수  |
| :----------------- | :------------------------------ | :----------------- | :---- |
| (Humanoid 본 이름) | `humanoid.humanBones.humanBone` | 하나의 Humanoid 본 | Mixed |

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.schema.json)

#### humanoid.humanBones.(Humanoid 본 이름)

하나의 Humanoid 본을 나타내는 객체입니다.
`VRMC_vrm` 확장에서 정의되어 있는 Humanoid 본 이름을 키 이름으로 가집니다(`hips`나 `leftUpperArm` 등).
단, Humanoid 본 중 `leftEye`와 `rightEye`는 정의할 수 없습니다.

- 타입: `humanoid.humanBones.humanBone`
- 필수: VRM 사양에서 필수 본으로 정의되어 있는 경우, 이 값은 필수입니다.

### humanoid.humanBones.humanBone

하나의 Humanoid 본을 나타내는 객체입니다.

#### Properties

|        | 타입      | 설명                                 | 필수   |
| :----- | :-------- | :----------------------------------- | :----- |
| `node` | `integer` | Humanoid 본에 대응하는 노드의 인덱스 | ✅ Yes |

#### JSON Schema

[VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json](schema/VRMC_vrm_animation.humanoid.humanBones.humanBone.schema.json)

#### humanoid.humanBones.humanBone.node ✅

Humanoid 본에 대응하는 노드의 인덱스입니다.

- 타입: `integer`
- 필수: Yes

---

### expressions

Expressions 표정과 노드의 대응 관계를 나타내는 객체입니다.

#### Properties

|          | 타입                 | 설명                    | 필수 |
| :------- | :------------------- | :---------------------- | :--- |
| `preset` | `expressions.preset` | 프리셋 표정에 대한 정의 | No   |
| `custom` | `expressions.custom` | 커스텀 표정에 대한 정의 | No   |

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset

프리셋 표정에 대한 정의를 포함하는 객체입니다.

- 타입: `expressions.preset`
- 필수: No

#### expressions.custom

커스텀 표정에 대한 정의를 포함하는 객체입니다.

- 타입: `expressions.custom`
- 필수: No

### expressions.preset

프리셋 표정에 대한 정의를 포함하는 객체입니다.

#### Properties

|                    | 타입                     | 설명               | 필수 |
| :----------------- | :----------------------- | :----------------- | :--- |
| (프리셋 표정 이름) | `expressions.expression` | 하나의 프리셋 표정 | No   |

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.preset.(프리셋 표정 이름)

하나의 프리셋 표정을 나타내는 객체입니다.
`VRMC_vrm` 확장에서 정의되어 있는 프리셋 표정의 이름을 키 이름으로 가집니다(`aa`나 `leftBlink` 등).
단, 프리셋 표정 중 `lookUp`, `lookDown`, `lookLeft`, `lookRight`는 정의할 수 없습니다.

- 타입: `expressions.expression`
- 필수: No

### expressions.custom

커스텀 표정에 대한 정의를 포함하는 객체입니다.

#### Properties

|                    | 타입                     | 설명               | 필수 |
| :----------------- | :----------------------- | :----------------- | :--- |
| (커스텀 표정 이름) | `expressions.expression` | 하나의 커스텀 표정 | No   |

#### JSON Schema

[VRMC_vrm_animation.expressions.schema.json](schema/VRMC_vrm_animation.expressions.schema.json)

#### expressions.custom.(커스텀 표정 이름)

하나의 커스텀 표정을 나타내는 객체입니다.
`VRMC_vrm` 확장에서 정의되는 프리셋 표정의 이름 이외라면 임의의 표정 이름을 지정할 수 있습니다.

- 타입: `expressions.expression`
- 필수: No

### expressions.expression

하나의 표정을 나타내는 객체입니다.

#### Properties

|        | 타입      | 설명                          | 필수   |
| :----- | :-------- | :---------------------------- | :----- |
| `node` | `integer` | 표정에 대응하는 노드의 인덱스 | ✅ Yes |

#### JSON Schema

[VRMC_vrm_animation.expressions.expression.schema.json](schema/VRMC_vrm_animation.expressions.expression.schema.json)

#### expressions.expression.node ✅

표정에 대응하는 노드의 인덱스입니다.

- 타입: `integer`
- 필수: Yes

---

### lookAt

시점·시선에 관한 정의를 수행하는 객체입니다.

#### Properties

|                      | 타입        | 설명                                             | 필수 |
| :------------------- | :---------- | :----------------------------------------------- | :--- |
| `node`               | `integer`   | 시선 방향에 대응하는 노드의 인덱스               | No   |
| `offsetFromHeadBone` | `number[3]` | Humanoid의 head로부터 시점 위치의 오프셋(offset) | No   |

#### JSON Schema

[VRMC_vrm_animation.lookAt.schema.json](schema/VRMC_vrm_animation.lookAt.schema.json)

#### lookAt.node

시선 방향에 대응하는 노드의 인덱스입니다.

- 타입: `integer`
- 필수: No

#### lookAt.offsetFromHeadBone

Humanoid의 head로부터 시점 위치의 오프셋(offset)입니다.

Humanoid가 정의되지 않은 경우 `offsetFromHeadBone`의 값을 그대로 시점 위치로 해석합니다.

- 타입: `number[3]`
- 필수: No
