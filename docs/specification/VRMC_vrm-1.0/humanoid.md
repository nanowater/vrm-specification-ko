# `VRMC_vrm.humanoid`

본 문서에서는 `VRMC_vrm` 확장 중 `humanoid` 필드에 대한 사양을 설명합니다.

`humanoid bone`의 목록을 정의합니다.

```json
extensions.VRMC_vrm.humanoid = {
  "humanBones": {
    "hips": {
      "node": 1 // glTF.Node의 인덱스
    },
    "spine": {
      "node": 2
    },
    // 생략
  }
}
```

## 휴머노이드 본 목록

- 휴머노이드 본은 VRM 내에서 동일한 것이 여러 개 존재해서는 안 됩니다.
- 표의 `-`는 부모 본이 반드시 존재하므로, 조건을 고려할 필요가 없음을 나타냅니다.

### 몸통 (Torso)

| 본 이름    | 필수 여부 | 부모 본    | 위치 기준   | 부모 본의 존재 필수 | 비고                                                                  |
| :--------- | :-------- | :--------- | ----------- | ------------------- | --------------------------------------------------------------------- |
| hips       | 필수      | (root)     | 사타구니    | -                   | 보통 이 본만 이동(Translation)하고, 다른 본은 회전(Rotation)만 합니다 |
| spine      | 필수      | hips       | 골반 상단   | -                   |                                                                       |
| chest      |           | spine      | 흉곽 하단   | -                   | 0.X에서는 필수였습니다                                                |
| upperChest |           | chest      |             | Yes                 | chest가 존재하는 경우에만 이 본이 존재할 수 있습니다                  |
| neck       |           | upperChest | 목의 밑부분 | No                  | 0.X에서는 필수였습니다                                                |

### 머리 (Head)

| 본 이름  | 필수 | 부모 본 | 위치 기준 | 부모 본의 존재 필수 | 비고                                |
| :------- | :--- | :------ | --------- | ------------------- | ----------------------------------- |
| head     | 필수 | neck    | 목 상단   | No                  |                                     |
| leftEye  |      | head    |           | -                   | 본(Bone) 제어 방식의 모델 안구 이동 |
| rightEye |      | head    |           | -                   | 본(Bone) 제어 방식의 모델 안구 이동 |
| jaw      |      | head    |           | -                   |                                     |

### 다리 (Legs)

| 본 이름       | 필수 | 부모 본       | 위치 기준          | 부모 본의 존재 필수 | 비고 |
| :------------ | :--- | :------------ | ------------------ | ------------------- | ---- |
| leftUpperLeg  | 필수 | hips          | 다리 연결부(groin) | -                   |      |
| leftLowerLeg  | 필수 | leftUpperLeg  | 무릎               | -                   |      |
| leftFoot      | 필수 | leftLowerLeg  | 발목               | -                   |      |
| leftToes      |      | leftFoot      | 발가락 연결부      | -                   |      |
| rightUpperLeg | 필수 | hips          | 다리 연결부(groin) | -                   |      |
| rightLowerLeg | 필수 | rightUpperLeg | 무릎               | -                   |      |
| rightFoot     | 필수 | rightLowerLeg | 발목               | -                   |      |
| rightToes     |      | rightFoot     | 발가락 연결부      | -                   |      |

### 팔 (Arms)

| 본 이름       | 필수 | 부모 본       | 위치 기준   | 부모 본의 존재 필수 | 비고 |
| :------------ | :--- | :------------ | ----------- | ------------------- | ---- |
| leftShoulder  |      | upperChest    |             | No                  |      |
| leftUpperArm  | 필수 | leftShoulder  | 윗팔 연결부 | No                  |      |
| leftLowerArm  | 필수 | leftUpperArm  | 팔꿈치      | -                   |      |
| leftHand      | 필수 | leftLowerArm  | 손목        | -                   |      |
| rightShoulder |      | upperChest    |             | No                  |      |
| rightUpperArm | 필수 | rightShoulder | 윗팔 연결부 | No                  |      |
| rightLowerArm | 필수 | rightUpperArm | 팔꿈치      | -                   |      |
| rightHand     | 필수 | rightLowerArm | 손목        | -                   |      |

### 손가락 (Fingers)

| 본 이름                 | 필수 | 부모 본                 | 위치 기준 | 부모 본의 존재 필수 | 비고 |
| :---------------------- | :--- | :---------------------- | --------- | ------------------- | ---- |
| leftThumbMetacarpal     |      | leftHand                |           | -                   |      |
| leftThumbProximal       |      | leftThumbMetacarpal     |           | Yes                 |      |
| leftThumbDistal         |      | leftThumbProximal       |           | Yes                 |      |
| leftIndexProximal       |      | leftHand                |           | -                   |      |
| leftIndexIntermediate   |      | leftIndexProximal       |           | Yes                 |      |
| leftIndexDistal         |      | leftIndexIntermediate   |           | Yes                 |      |
| leftMiddleProximal      |      | leftHand                |           | -                   |      |
| leftMiddleIntermediate  |      | leftMiddleProximal      |           | Yes                 |      |
| leftMiddleDistal        |      | leftMiddleIntermediate  |           | Yes                 |      |
| leftRingProximal        |      | leftHand                |           | -                   |      |
| leftRingIntermediate    |      | leftRingProximal        |           | Yes                 |      |
| leftRingDistal          |      | leftRingIntermediate    |           | Yes                 |      |
| leftLittleProximal      |      | leftHand                |           | -                   |      |
| leftLittleIntermediate  |      | leftLittleProximal      |           | Yes                 |      |
| leftLittleDistal        |      | leftLittleIntermediate  |           | Yes                 |      |
| rightThumbMetacarpal    |      | rightHand               |           | -                   |      |
| rightThumbProximal      |      | rightThumbMetacarpal    |           | Yes                 |      |
| rightThumbDistal        |      | rightThumbProximal      |           | Yes                 |      |
| rightIndexProximal      |      | rightHand               |           | -                   |      |
| rightIndexIntermediate  |      | rightIndexProximal      |           | Yes                 |      |
| rightIndexDistal        |      | rightIndexIntermediate  |           | Yes                 |      |
| rightMiddleProximal     |      | rightHand               |           | -                   |      |
| rightMiddleIntermediate |      | rightMiddleProximal     |           | Yes                 |      |
| rightMiddleDistal       |      | rightMiddleIntermediate |           | Yes                 |      |
| rightRingProximal       |      | rightHand               |           | -                   |      |
| rightRingIntermediate   |      | rightRingProximal       |           | Yes                 |      |
| rightRingDistal         |      | rightRingIntermediate   |           | Yes                 |      |
| rightLittleProximal     |      | rightHand               |           | -                   |      |
| rightLittleIntermediate |      | rightLittleProximal     |           | Yes                 |      |
| rightLittleDistal       |      | rightLittleIntermediate |           | Yes                 |      |

## 휴머노이드 본의 계층(부모-자식) 관계

- 휴머노이드 본은 지정된 부모 본이 정해져 있습니다. 필수 항목이 아닌 부모 본이 존재하지 않는 경우, 그 부모 본의 부모 본(상위 본)을 찾아 연결합니다.
- 휴머노이드 본 사이에 휴머노이드 본이 아닌 일반 노드가 들어가는 것은 허용됩니다. (예: UpperLeg와 LowerLeg 사이에 휴머노이드 본이 아닌 노드가 존재하는 경우 등)

hips를 root로 하여 다음과 같은 부모 자식 관계를 가집니다.

- root(휴머노이드 본이 아님. 원점)
  - hips
    - spine
      - (chest)
        - (upperChest)
          - (neck)
            - head
              - (leftEye)
              - (rightEye)
              - (jaw)
          - (leftShoulder)
            - leftUpperArm
              - leftLowerArm
                - leftHand
                  - (leftFingers)...
          - (rightShoulder)
            - rightUpperArm
              - rightLowerArm
                - rightHand
                  - (rightFingers)...
    - leftUpperLeg
      - leftLowerLeg
        - leftFoot
          - (leftToes)
    - rightUpperLeg
      - rightLowerLeg
        - rightFoot
          - (rightToes)
