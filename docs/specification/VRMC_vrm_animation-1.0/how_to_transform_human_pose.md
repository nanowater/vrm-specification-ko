# 포즈 데이터의 호환성에 대하여

> 이 문서는 Non-normative(비규범적)입니다.

`VRMC_vrm_animation`에서는 인간형 모델(Humanoid)의 포즈 데이터를 다룹니다.
이 때, 다른 T-pose를 가진 모델 간에 포즈 데이터의 호환성 문제가 발생할 수 있습니다.
이 문서에서는 다른 T-pose를 가진 모델 간에 포즈 데이터의 호환성을 해결하는 방법을 설명합니다.

## 용어 도입

### Humanoid
이 문서에서 다루는 *Humanoid*는 VRM의 Humanoid 정의에 의존합니다.
[VRM 사양 내의 Humanoid의 정의](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/humanoid.ja.md)를 참조하십시오.

### T-pose
이 문서에서 다루는 *T-pose*는 VRM의 T-pose에 의존합니다.
[VRM T-pose: VRM이 정의하는 자세 사양](https://github.com/vrm-c/vrm-specification/blob/master/specification/VRMC_vrm-1.0/tpose.ja.md)을 참조하십시오.

### 레스트 회전 (Rest Rotation)
VRM-1.0이 정의하는 T-pose 상태에서의 노드(node)의 회전 상태를 **레스트 회전**이라고 부릅니다.

VRM-0.X에서는 레스트 회전이 무회전임을 사양화하여 포즈 데이터의 호환성 문제가 발생하지 않도록 했습니다.
VRM-1.0에서는 이 레스트 회전의 제약을 철폐했습니다.

### 비필수 본 (Non-required Bone)
VRM에서 정의된 Humanoid 본 중, 필수가 아닌 **비필수 본**은 모델 간에 존재 유무가 다를 수 있습니다.

예를 들어, `hips`나 `head` 등의 본은 필수 본인 반면, `upperChest`나 `leftShoulder` 등은 비필수 본입니다.
이로 인해 `upperChest` 본이 있는 모델과 없는 모델이 모두 존재합니다.

### 동일한 자세
각 `HumanBone`이 외형적으로 동일한 방향을 향할 때 **동일한 자세**라고 합니다.
T-pose 시에 약간 비스듬하게 되어 있는 등의 미묘한 차이는 포함되므로, 완전히 동일한 자세가 되지는 않습니다.

### 포즈 데이터
**포즈 데이터**는 `hips` 본의 평행 이동 값과 모든 Humanoid 본의 로컬 회전으로 표현됩니다.

레스트 회전의 영향을 받아, 동일한 자세에 대한 수치가 변화합니다.
또한 비필수 본의 유무의 영향을 받아, 동일한 자세에 대한 수치가 변화합니다.

**T-pose의 레스트 회전 또는 비필수 본의 유무가 다를 경우, 동일한 자세에 대한 포즈 데이터는 호환되지 않습니다.**

## 포즈 데이터의 호환성을 갖게 하는 변환 처리

다른 T-pose를 가진 모델 간에 포즈 데이터의 호환성을 갖게 하기 위해, 다음 중 하나의 변환 처리가 필요합니다.

### 모델을 변환한다
모델의 레스트 회전을 무회전으로 변환합니다.

VRM-0.X의 UniVRM에서는 모델 익스포트(export) 시 모델의 레스트 회전을 무회전으로 하여 출력했습니다.

### 포즈 데이터를 변환한다
포즈 데이터를 임의의 레스트 회전을 가진 모델에 적용할 수 있도록 변환합니다.

VRM-1.0에서는 모델이 임의의 레스트 회전을 가지므로 이 방법이 예상됩니다.

VRM-1.0의 UniVRM에서는 런타임(runtime)에 포즈 데이터의 변환을 수행할 수 있도록 ControlRig라는 기능이 제공됩니다.

## 변환 처리의 상세

- `TPoseA`: 모델 A의 레스트 회전
- `PoseForA`: 모델 A에 적용했을 때 의도한 외형이 되는 포즈 데이터
- `TPoseB`: 모델 B의 레스트 회전

이 있을 때, `PoseForB`: 모델 B에 적용했을 때 `PoseForA`와 동일한 자세가 되는 포즈 데이터를 얻는 방법을 설명합니다.

### 중간 형식 NormalizedLocalRotation

여기서 처리를 단순화하기 위해 중간 형식 `NormalizedLocalRotation`을 도입합니다.

`PoseForA` => `NormalizedLocalRotation` => `PoseForB`

`NormalizedLocalRotation`은 레스트 회전이 무회전인 모델에 적용했을 때, `PoseForA`와 동일한 자세가 되는 포즈 데이터로 합니다.

> VRM-0.X의 정규화 상태와 거의 같습니다. VRM-1.0에서 T-pose의 정의를 명확히 했으므로 동일하다고 단정할 수는 없습니다.
>
> `TPoseA`가 무회전일 경우는 `PoseForA`와 `NormalizedLocalRotation`이 같아져 간단해집니다.
> 마찬가지로 `TPoseB`가 무회전일 경우는 `NormalizedLocalRotation`과 `PoseForB`가 같아져 간단해집니다.

### `PoseForA` => `NormalizedLocalRotation`

- W: TPoseA의 World 레스트 회전
- L: TPoseA의 Local 레스트 회전

$NormalizedLocalRotation = W \cdot L^{-1} \cdot A.LocalRotation \cdot W^{-1}$

```cs
// C#
InitialGlobalRotation * Quaternion.Inverse(InitialLocalRotation) * Transform.localRotation * Quaternion.Inverse(InitialGlobalRotation);
```

### `NormalizedLocalRotation` => `PoseForB`

- W: TPoseB의 World 레스트 회전
- L: TPoseB의 Local 레스트 회전

$B.LocalRotation = L \cdot W^{-1} \cdot NormalizedLocalRotation \cdot W$

```cs
// C#
ControlTarget.localRotation = _initialTargetLocalRotation * (Quaternion.Inverse(_initialTargetGlobalRotation) * ControlBone.localRotation * _initialTargetGlobalRotation);
```

### 비필수 본의 유무가 다른 경우
변환 원본 모델과 변환 대상 모델 간에 비필수 본의 유무가 다른 경우의 변환 처리에 대해 설명합니다.

#### 변환 원본 모델의 본이 적은 경우
변환 원본 모델의 본이 적은 경우, 단순히 변환 원본 모델에 포함된 본만 적용하는 것이 권장됩니다.

#### 변환 대상 모델의 본이 적은 경우
변환 대상 모델의 본이 적은 경우, 본래 대상 본의 회전에 의해 영향을 주어야 했던 자식 본(child bone) 전체에 대해 대상 본의 회전을 적용하는 것이 권장됩니다.
예를 들어, upperChest가 변환 원본 모델에 존재하고 변환 대상 모델에 존재하지 않는 경우, 변환 대상 모델의 neck, leftShoulder, rightShoulder에는 upperChest의 회전과 해당 본 자체의 회전을 모두 곱하여 적용하는 것이 바람직합니다.
즉, 변환 원본 애니메이션에서의 본별 외형 자세와 변환 대상 모델에서의 대응하는 본의 외형 자세가 같은 방향이 될 것으로 기대합니다.

### 이동량의 스케일링에 대하여
크기가 다른 모델 간에는 hips 본의 이동량을 스케일(scale)하면 외형이 자연스러워질 것으로 기대할 수 있습니다.
하나의 아이디어로 다음의 식이 됩니다.

변환 원본 모델의 T 포즈 시의 hips 높이를 `y_src`, 변환 대상 모델의 T 포즈 시의 hips 높이를 `y_dst`라고 할 때, hips의 이동량에 곱할 스케일 `scaling_factor = y_dst / y_src`

각 본이 동일한 회전을 하는 모델 간에는 이동량이 다리 길이에 비례할 것이라는 생각입니다.
hips의 높이를 다리 길이로 간주합니다.
