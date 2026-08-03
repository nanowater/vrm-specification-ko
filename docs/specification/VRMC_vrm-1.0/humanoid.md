# `VRMC_vrm.humanoid`

본 문서에서는 `VRMC_vrm` 확장 중 `humanoid` 필드에 대한 사양을 설명합니다.
`humanoid bone`의 목록을 정의합니다.

```json
extensions.VRMC_vrm.humanoid = {
  "humanBones": {
    "hips": {
      "node": 1 // index of glTF.Node
    },
    "spine": {
      "node": 2
    },
    // 생략
  }
}
```

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [휴머노이드 본 목록](#%ED%9C%B4%EB%A8%B8%EB%85%B8%EC%9D%B4%EB%93%9C-%EB%B3%B8-%EB%AA%A9%EB%A1%9D)
  - [몸통](#%EB%AA%B8%ED%86%B5)
  - [머리](#%EB%A8%B8%EB%A6%AC)
  - [다리](#%EB%8B%A4%EB%A6%AC)
  - [팔](#%ED%8C%94)
  - [손가락](#%EC%86%90%EA%B0%80%EB%9D%BD)
- [휴머노이드 본의 부모 자식 관계](#%ED%9C%B4%EB%A8%B8%EB%85%B8%EC%9D%B4%EB%93%9C-%EB%B3%B8%EC%9D%98-%EB%B6%80%EB%AA%A8-%EC%9E%90%EC%8B%9D-%EA%B4%80%EA%B3%84)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 휴머노이드 본 목록

* 휴머노이드 본은 VRM 내에서 동일한 것이 여러 개 존재해서는 안 됩니다.
* 표의 `-`는 부모 본이 반드시 존재하므로, 조건을 고려할 필요가 없음을 나타냅니다.

### 몸통

| 본 이름 | 필수 |  부모 본  | 위치 기준 | 부모 본의 존재 필수 |                           비고                           |
| :--------- | :--- | :--------- | ---------- | -------------------- | -------------------------------------------------------- |
| hips       | 필수 | (root)     | 사타구니       | -                    | 보통 이 본만 이동하고 다른 본들은 회전만 합니다 |
| spine      | 필수 | hips       | 골반 상단 | -                    |                                                          |
| chest      |      | spine      | 흉곽 하단 | -                    | 0.X에서는 필수였습니다                                       |
| upperChest |      | chest      |            | Yes                  | chest가 존재하는 경우에만 이 본이 존재할 수 있습니다     |
| neck       |      | upperChest | 목의 밑부분 | No                   | 0.X에서는 필수였습니다                                       |

### 머리

| 본 이름 | 필수 | 부모 본 | 위치 기준 | 부모 본의 존재 필수 |                                                                    비고                                                                     |
| :--------- | :--- | :------- | ---------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| head       | 필수 | neck     | 목 상단   | No                   |                                                                                                                                             |
| leftEye    |      | head     |            | -                    | [`VRMC_vrm.lookAt` 시선 제어(옵션)](#vrmc_vrmlookat-%EC%8B%9C%EC%84%A0%EC%A0%9C%EC%96%B4%EC%98%B5%EC%85%98) |
| rightEye   |      | head     |            | -                    | [`VRMC_vrm.lookAt` 시선 제어(옵션)](#vrmc_vrmlookat-%EC%8B%9C%EC%84%A0%EC%A0%9C%EC%96%B4%EC%98%B5%EC%85%98) |
| jaw        |      | head     |            | -                    |                                                                                                                                             |

### 다리

|  본 이름   | 필수 |   부모 본    |   위치 기준   | 부모 본의 존재 필수 | 비고 |
| :------------ | :--- | :------------ | -------------- | -------------------- | ---- |
| leftUpperLeg  | 필수 | hips          | 다리의 밑부분     | -                    |      |
| leftLowerLeg  | 필수 | leftUpperLeg  | 무릎             | -                    |      |
| leftFoot      | 필수 | leftLowerLeg  | 발목           | -                    |      |
| leftToes      |      | leftFoot      | 발가락의 밑부분 | -                    |      |
| rightUpperLeg | 필수 | hips          | 다리의 밑부분     | -                    |      |
| rightLowerLeg | 필수 | rightUpperLeg | 무릎             | -                    |      |
| rightFoot     | 필수 | rightLowerLeg | 발목           | -                    |      |
| rightToes     |      | rightFoot     | 발가락의 밑부분 | -                    |      |

### 팔

|  본 이름   | 필수 |   부모 본    |  위치 기준  | 부모 본의 존재 필수 | 비고 |
| :------------ | :--- | :------------ | ------------ | -------------------- | ---- |
| leftShoulder  |      | upperChest    |              | No                   |      |
| leftUpperArm  | 필수 | leftShoulder  | 위팔의 밑부분 | No                   |      |
| leftLowerArm  | 필수 | leftUpperArm  | 팔꿈치           | -                    |      |
| leftHand      | 필수 | leftLowerArm  | 손목         | -                    |      |
| rightShoulder |      | upperChest    |              | No                   |      |
| rightUpperArm | 필수 | rightShoulder | 위팔의 밑부분 | No                   |      |
| rightLowerArm | 필수 | rightUpperArm | 팔꿈치           | -                    |      |
| rightHand     | 필수 | rightLowerArm | 손목         | -                    |      |

### 손가락

|       본 이름        | 필수 |        부모 본         | 위치 기준 | 부모 본의 존재 필수 | 비고 |
| :---------------------- | :--- | :---------------------- | ---------- | -------------------- | ---- |
| leftThumbMetacarpal     |      | leftHand                |            | -                    |      |
| leftThumbProximal       |      | leftThumbMetacarpal     |            | Yes                  |      |
| leftThumbDistal         |      | leftThumbProximal       |            | Yes                  |      |
| leftIndexProximal       |      | leftHand                |            | -                    |      |
| leftIndexIntermediate   |      | leftIndexProximal       |            | Yes                  |      |
| leftIndexDistal         |      | leftIndexIntermediate   |            | Yes                  |      |
| leftMiddleProximal      |      | leftHand                |            | -                    |      |
| leftMiddleIntermediate  |      | leftMiddleProximal      |            | Yes                  |      |
| leftMiddleDistal        |      | leftMiddleIntermediate  |            | Yes                  |      |
| leftRingProximal        |      | leftHand                |            | -                    |      |
| leftRingIntermediate    |      | leftRingProximal        |            | Yes                  |      |
| leftRingDistal          |      | leftRingIntermediate    |            | Yes                  |      |
| leftLittleProximal      |      | leftHand                |            | -                    |      |
| leftLittleIntermediate  |      | leftLittleProximal      |            | Yes                  |      |
| leftLittleDistal        |      | leftLittleIntermediate  |            | Yes                  |      |
| rightThumbMetacarpal    |      | rightHand               |            | -                    |      |
| rightThumbProximal      |      | rightThumbMetacarpal    |            | Yes                  |      |
| rightThumbDistal        |      | rightThumbProximal      |            | Yes                  |      |
| rightIndexProximal      |      | rightHand               |            | -                    |      |
| rightIndexIntermediate  |      | rightIndexProximal      |            | Yes                  |      |
| rightIndexDistal        |      | rightIndexIntermediate  |            | Yes                  |      |
| rightMiddleProximal     |      | rightHand               |            | -                    |      |
| rightMiddleIntermediate |      | rightMiddleProximal     |            | Yes                  |      |
| rightMiddleDistal       |      | rightMiddleIntermediate |            | Yes                  |      |
| rightRingProximal       |      | rightHand               |            | -                    |      |
| rightRingIntermediate   |      | rightRingProximal       |            | Yes                  |      |
| rightRingDistal         |      | rightRingIntermediate   |            | Yes                  |      |
| rightLittleProximal     |      | rightHand               |            | -                    |      |
| rightLittleIntermediate |      | rightLittleProximal     |            | Yes                  |      |
| rightLittleDistal       |      | rightLittleIntermediate |            | Yes                  |      |

## 휴머노이드 본의 부모 자식 관계

* 휴머노이드 본은 부모 본이 정해져 있습니다. 필수가 아닌 부모 본이 존재하지 않는 경우, 부모 본의 부모 본을 찾으십시오.
* 휴머노이드 본 사이에 휴머노이드 본이 아닌 노드가 들어가는 것은 허용됩니다 (UpperLeg와 LowerLeg 사이에 휴머노이드 본이 아닌 노드가 있는 등).

hips를 root로 하여 다음과 같은 부모 자식 관계가 됩니다.

* root(휴머노이드 본이 아님. 원점)
  * hips
    * spine
      * (chest)
        * (upperChest)
          * (neck)
            * head
              * (leftEye)
              * (rightEye)
              * (jaw)
          * (leftShoulder)
            * leftUpperArm
              * leftLowerArm
                * leftHand
                  * (leftFingers)...
          * (rightShoulder)
            * rightUpperArm
              * rightLowerArm
                * rightHand
                  * (rightFingers)...
    * leftUpperLeg
      * leftLowerLeg
        * leftFoot
          * (leftToes)
    * rightUpperLeg
      * rightLowerLeg
        * rightFoot
          * (rightToes)
