# VRMC_node_constraint

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

_Version 1.0_

## Contributors

- 신도 테츠로
- 소 하쿠쇼
- 오부치 유타카

## Status

Complete

## Dependencies

glTF 2.0 사양을 바탕으로 작성되었습니다.

## Overview

이 확장은 glTF 씬(scene) 내의 특정 Node의 Transform을 다른 Node에 의해 제약(Constraint)하는 것을 가능하게 합니다.

이 확장에서는 Roll Constraint, Aim Constraint, Rotation Constraint를 정의하고 있습니다.

### Purposes

VRM에서 다루는 컨스트레인트(Constraint)는 실시간으로 [Humanoid 본](../VRMC_vrm-1.0/humanoid.md)에 할당되는 회전 정보를 사용하여 보조 본을 제어하는 유스케이스를 염두에 두고 설계되었습니다.

또한, 플랫폼을 넘나들며 컨스트레인트가 사용될 것을 고려하여 용도에 특화된 의미론적(semantic) 정의를 수행합니다.
따라서, 구현 시 아티스트가 본래 달성하고자 했던 결과를 얻을 수 있기를 기대하며, 동작이 여러 구현 간에 완전히 일치하는 것을 목표로 하지는 않습니다.

## Constraints

본 확장에서는 3가지 컨스트레인트 _Roll Constraint_, _Aim Constraint_, *Rotation Constraint*가 정의되어 있습니다.

### Sources

각 Constraint는 제약되는 _Destination_ 노드와 그것을 제약하는 _Source_ 노드를 각각 하나씩 지정합니다.

Node가 Constraint의 Source가 되기 위해서는 다음 조건이 필요합니다:

- Source는 Destination 노드 그 자체여서는 안 됩니다.
- Source는 다른 컨스트레인트와 결합하여 순환 의존 관계를 형성해서는 안 됩니다.

### Weight

각 Constraint는 회전량을 어느 정도 Destination에 전달할지를 나타내는 *Weight*를 지정합니다.

Weight는 [0.0 - 1.0]의 수치로 표현되며, Destination의 레스트(rest) 회전에서 Constraint에 의해 결정되는 회전으로의 구면 선형 보간(Spherical Linear Interpolation, slerp)을 수행합니다.

### Roll Constraint

Roll Constraint는 Source의 회전 중 특정 단일 축의 회전만을 Destination에 전달하기 위해 사용하는 컨스트레인트입니다.

#### Purposes

Roll Constraint는 다음과 같은 용도로 사용될 것을 상정합니다:

- 팔・다리의 트위스트 본(Twist bone)

#### Hierarchy

Roll Constraint는 예를 들어 다음과 같은 구조로 사용될 것을 전제로 합니다:

```markdown
- LowerArm
  - Hand
  - Twist1 (RollConstraint, Source is LowerArm)
  - Twist2 (RollConstraint, Source is LowerArm)
```

#### Roll Axis

Roll Constraint는 *Roll Axis*를 한 축으로 지정할 수 있으며, 이를 통해 Destination의 회전 중 어느 축을 Source에 전달할지 지정합니다.

Roll Axis에는 `"X"`, `"Y"`, `"Z"` 중 하나를 지정합니다.

#### Evaluation of rotations

Source의 회전 평가는 Source의 레스트 상태를 기준으로 Destination의 레스트 상태에서의 Roll Axis 주위의 회전을 평가하는 것이 권장됩니다.
또한 Source가 Roll Axis 주위 이외로 회전하는 경우, Roll Axis가 그 회전과 같은 방향을 향하는 최소 회전과의 차이를 사용하여 롤(Roll) 회전 평가를 수행하는 것이 권장됩니다.

#### Example of Implementation

> _이 섹션은 Non-Normative입니다._

아래에 의사 코드(Pseudo-code)로 구현 예를 나타냅니다:

```js
deltaSrcQuat = srcRestQuat.inverse * srcQuat;
deltaSrcQuatInParent = srcRestQuat * deltaSrcQuat * srcRestQuat.inverse; // source to parent
deltaSrcQuatInDst = dstRestQuat.inverse * deltaSrcQuatInParent * dstRestQuat; // parent to destination

toVec = rollAxis.applyQuaternion(deltaSrcQuatInDst);
fromToQuat = Quaternion.fromToRotation(rollAxis, toVec);

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstRestQuat * fromToQuat.inverse * deltaSrcQuatInDst,
  weight,
);
```

### Aim Constraint

Aim Constraint는 Destination이 Source의 방향을 향하도록 회전시키기 위해 사용하는 컨스트레인트입니다.

#### Purposes

Aim Constraint는 다음과 같은 용도로 사용될 것을 상정합니다:

- 의상의 소매

#### Hierarchy

Aim Constraint는 예를 들어 다음과 같은 구조로 사용될 것을 전제로 합니다:

```markdown
- UpperArm
  - LowerArm
- Aim (AimConstraint, Source is LowerArm)
```

#### Aim Axis

Aim Constraint는 *Aim Axis*를 한 방향으로 지정할 수 있으며, 이를 통해 Destination의 어느 축이 Source의 방향을 향하도록 할지 지정합니다.

Aim Axis에는 `"PositiveX"`, `"NegativeX"`, `"PositiveY"`, `"NegativeY"`, `"PositiveZ"`, `"NegativeZ"` 중 하나를 지정합니다.

#### Evaluation of rotations

Destination의 회전은, Destination이 레스트 상태에서 Destination의 Aim Axis가 월드 공간에서 Destination으로부터 Source를 향해 뻗어 나가는 벡터의 방향을 향하도록 하는 최소의 회전으로 설정하는 것이 권장됩니다.

#### Example of Implementation

> _이 섹션은 Non-Normative입니다._

아래에 의사 코드로 구현 예를 나타냅니다:

```js
fromVec = aimAxis.applyQuaternion(dstParentWorldQuat * dstRestQuat);
toVec = (srcWorldPos - dstWorldPos).normalized;
fromToQuat = Quaternion.fromToRotation(fromVec, toVec);

targetQuat = Quaternion.slerp(
  dstRestQuat,
  dstParentWorldQuat.inverse * fromToQuat * dstParentWorldQuat * dstRestQuat,
  weight,
);
```

### Rotation Constraint

Rotation Constraint는 Source의 회전을 Destination의 회전으로 전달하기 위한 컨스트레인트입니다.

본 확장으로 정의되는 Rotation Constraint는 Local-Local이 됩니다.

#### Purposes

Rotation Constraint는 다음과 같은 용도로 사용될 것을 상정합니다:

- 서브 암(Sub-arm)

#### Evaluation of rotations

Source의 회전은 Source가 레스트 상태에서 Source의 오리엔테이션으로 어떻게 로컬에서 회전했는지를 관측하고, 그것을 Destination의 레스트 상태를 기준으로 Destination의 오리엔테이션에서 로컬로 회전시키는 것이 권장됩니다.

> Blender의 Bone Constraint에서의 Local-Local Copy Rotation과 같은 동작이 기대됩니다.

#### Example of Implementation

> _이 섹션은 Non-Normative입니다._

아래에 의사 코드로 구현 예를 나타냅니다:

```js
srcDeltaQuat = srcRestQuat.inverse * srcQuat;

targetQuat = Quaternion.slerp(dstRestQuat, dstRestQuat * srcDeltaQuat, weight);
```

---

## glTF Schema Updates

### Extending Nodes

컨스트레인트는 node에 `VRMC_node_constraint` 확장을 추가하여 기술됩니다.
다음은 `NodeB`를 `NodeA`로 제약하는 Rotation Constraint의 기술 예입니다:

```json
{
  "extensionsUsed": {
    "VRMC_node_constraint"
  },
  "nodes": [
    {
      "name": "NodeA",
    },
    {
      "name": "NodeB",
      // node.extensions
      "extensions": {
        "VRMC_node_constraint": {
          "specVersion": "1.0",
          "constraint": {
            "rotation": {
              "source": 0,
              "weight": 1.0
            }
          }
        }
      }
    }
  ],
  // 일반적인 GLTF-2.0 정보
  "materials": [
    {
      // ...
    }
  ]
}
```

---

### VRMC_node_constraint

본 확장의 루트(root) 오브젝트입니다.

#### Properties

|               | 타입     | 설명                                  | 필수   |
| :------------ | :------- | :------------------------------------ | :----- |
| `specVersion` | `string` | 본 확장의 사양 버전을 나타냅니다.     | ✅ Yes |
| `constraint`  | `object` | Constraint를 나타내는 오브젝트입니다. | ✅ Yes |

- JSON schema: [VRMC_node_constraint.schema.json](./schema/VRMC_node_constraint.schema.json)

#### VRMC_node_constraint.specVersion ✅

VRMC_node_constraint 확장의 사양 버전을 나타냅니다.
값은 `"1.0"`입니다.

- 타입: `string`
- 필수: Yes

#### VRMC_node_constraint.constraint ✅

[Constraint](#constraint)를 나타내는 오브젝트입니다.

- 타입: `object`
- 필수: Yes

---

### constraint

컨스트레인트를 포함하는 오브젝트입니다.

`roll`, `aim`, `rotation` 중 어느 하나만을 포함해야 합니다.

#### Properties

|            | 타입     | 설명                              | 필수 |
| :--------- | :------- | :-------------------------------- | :--- |
| `roll`     | `object` | Roll Constraint를 기술합니다.     | No   |
| `aim`      | `object` | Aim Constraint를 기술합니다.      | No   |
| `rotation` | `object` | Rotation Constraint를 기술합니다. | No   |

- JSON schema: [VRMC_node_constraint.constraint.schema.json](./schema/VRMC_node_constraint.constraint.schema.json)

#### constraint.roll

[Roll Constraint](#rollConstraint)를 기술합니다.

- 타입: `object`
- 필수: No

#### constraint.aim

[Aim Constraint](#aimConstraint)를 기술합니다.

- 타입: `object`
- 필수: No

#### constraint.rotation

[Rotation Constraint](#rotationConstraint)를 기술합니다.

- 타입: `object`
- 필수: No

---

### rollConstraint

[Roll Constraint](#roll-constraint)를 기술하는 오브젝트입니다.

#### Properties

|            | 타입      | 설명                            | 필수              |
| :--------- | :-------- | :------------------------------ | :---------------- |
| `source`   | `integer` | 이 Node를 제약하는 Node의 Index | ✅ Yes            |
| `rollAxis` | `string`  | 이 Constraint의 Roll Axis       | ✅ Yes            |
| `weight`   | `number`  | 이 Constraint의 Weight          | No, 초기값: `1.0` |

- JSON schema: [VRMC_node_constraint.rollConstraint.schema.json](./schema/VRMC_node_constraint.rollConstraint.schema.json)

#### rollConstraint.source ✅

이 Node를 제약하는 Node의 Index를 지정합니다.

- 타입: `integer`
- 필수: Yes
- 최소값: `>= 0`

#### rollConstraint.rollAxis ✅

이 Constraint의 Roll Axis를 지정합니다.

- 타입: `string`
- 필수: Yes
- 허용된 값:
  - `X`
  - `Y`
  - `Z`

#### rollConstraint.weight

이 Constraint의 Weight를 지정합니다.

- 타입: `number`
- 필수: No, 초기값: `1.0`

---

### aimConstraint

[Aim Constraint](#aim-constraint)를 기술하는 오브젝트입니다.

#### Properties

|           | 타입      | 설명                            | 필수              |
| :-------- | :-------- | :------------------------------ | :---------------- |
| `source`  | `integer` | 이 Node를 제약하는 Node의 Index | ✅ Yes            |
| `aimAxis` | `string`  | 이 Constraint의 Aim Axis        | ✅ Yes            |
| `weight`  | `number`  | 이 Constraint의 Weight          | No, 초기값: `1.0` |

- JSON schema: [VRMC_node_constraint.aimConstraint.schema.json](./schema/VRMC_node_constraint.aimConstraint.schema.json)

#### aimConstraint.source ✅

이 Node를 제약하는 Node의 Index를 지정합니다.

- 타입: `integer`
- 필수: Yes
- 최소값: `>= 0`

#### aimConstraint.aimAxis ✅

이 Constraint의 Aim Axis를 지정합니다.

- 타입: `string`
- 필수: Yes
- 허용된 값:
  - `PositiveX`
  - `NegativeX`
  - `PositiveY`
  - `NegativeY`
  - `PositiveZ`
  - `NegativeZ`

#### aimConstraint.weight

이 Constraint의 Weight를 지정합니다.

- 타입: `number`
- 필수: No, 초기값: `1.0`

---

### rotationConstraint

[Rotation Constraint](#rotation-constraint)를 기술하는 오브젝트입니다.

#### Properties

|          | 타입      | 설명                            | 필수              |
| :------- | :-------- | :------------------------------ | :---------------- |
| `source` | `integer` | 이 Node를 제약하는 Node의 Index | ✅ Yes            |
| `weight` | `number`  | 이 Constraint의 Weight          | No, 초기값: `1.0` |

- JSON schema: [VRMC_node_constraint.rotationConstraint.schema.json](./schema/VRMC_node_constraint.rotationConstraint.schema.json)

#### rotationConstraint.source ✅

이 Node를 제약하는 Node의 Index를 지정합니다.

- 타입: `integer`
- 필수: Yes
- 최소값: `>= 0`

#### rotationConstraint.weight

이 Constraint의 Weight를 지정합니다.

- 타입: `number`
- 필수: No, 초기값: `1.0`

---

## Implementation Notes

> _이 섹션은 non-normative입니다._

### Dependency resolution between constraints

constraint는 다른 constraint에 의존하는 경우가 있습니다.
constraint의 처리 중에 아직 업데이트되지 않은 transform을 참조하는 것을 방지하기 위해, constraint의 갱신은 적절한 순서로 이루어져야 합니다.

아래 의사 코드는 constraint가 어떻게 업데이트되어야 하는지, 절차의 일례를 보여줍니다:

```
let constraintsPending = empty set of Constraint
let constraintsDone = empty set of Constraint

function updateConstraint( constraint: Constraint )
  if not constraintsDone.has( constraint ) then
    if constraintPending.has( constraint ) then
      throw "Circular dependency detected"
    end if

    constraintsPending.add( constraint )
    foreach dependency in constraint.dependencies do
      updateConstraint( constraint )
    end foreach
    constraintsPending.delete( constraint )

    constraint.update()

    constraintsDone.add( constraint )
  end if
end function

function updateConstraints
  foreach constraint in constraints do
    updateConstraint( constraint )
  end foreach
end function
```
