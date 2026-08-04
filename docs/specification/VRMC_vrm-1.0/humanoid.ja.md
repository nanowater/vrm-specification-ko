# `VRMC_vrm.humanoid`

本文書では、 `VRMC_vrm` 拡張のうち `humanoid` フィールドについての仕様を示します。
`humanoid bone` の一覧を定義します。

```json
extensions.VRMC_vrm.humanoid = {
  "humanBones": {
    "hips": {
      "node": 1 // index of glTF.Node
    },
    "spine": {
      "node": 2
    },
    // 省略
  }
}
```

## ヒューマノイドボーンの一覧

* ヒューマノイドボーンは VRM 内で同じものが複数存在してはいけません。
* 表中の `-` は親ボーンが必ず存在するため、条件を考慮する必要が無いことを示します。

### 胴

| ボーン名前 | 必須 |  親ボーン  | 位置の目安 | 親ボーンの存在が必須 |                           備考                           |
| :--------- | :--- | :--------- | ---------- | -------------------- | -------------------------------------------------------- |
| hips       | 必須 | (root)     | 股間       | -                    | 通常、このボーンだけが移動し、他のボーンは回転だけします |
| spine      | 必須 | hips       | 骨盤の上端 | -                    |                                                          |
| chest      |      | spine      | 胸郭の下端 | -                    | 0.X では必須でした                                       |
| upperChest |      | chest      |            | Yes                  | chest が存在する場合にのみ、このボーンは存在できます     |
| neck       |      | upperChest | 首の付け根 | No                   | 0.X では必須でした                                       |

### 頭

| ボーン名前 | 必須 | 親ボーン | 位置の目安 | 親ボーンの存在が必須 |                                                                    備考                                                                     |
| :--------- | :--- | :------- | ---------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| head       | 必須 | neck     | 首の上端   | No                   |                                                                                                                                             |
| leftEye    |      | head     |            | -                    | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| rightEye   |      | head     |            | -                    | [`VRMC_vrm.lookAt` 視線制御(オプション)](#vrmc_vrmlookat-%E8%A6%96%E7%B7%9A%E5%88%B6%E5%BE%A1%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3) |
| jaw        |      | head     |            | -                    |                                                                                                                                             |

### 脚

|  ボーン名前   | 必須 |   親ボーン    |   位置の目安   | 親ボーンの存在が必須 | 備考 |
| :------------ | :--- | :------------ | -------------- | -------------------- | ---- |
| leftUpperLeg  | 必須 | hips          | 脚の付け根     | -                    |      |
| leftLowerLeg  | 必須 | leftUpperLeg  | 膝             | -                    |      |
| leftFoot      | 必須 | leftLowerLeg  | 足首           | -                    |      |
| leftToes      |      | leftFoot      | 足の指の付け根 | -                    |      |
| rightUpperLeg | 必須 | hips          | 脚の付け根     | -                    |      |
| rightLowerLeg | 必須 | rightUpperLeg | 膝             | -                    |      |
| rightFoot     | 必須 | rightLowerLeg | 足首           | -                    |      |
| rightToes     |      | rightFoot     | 足の指の付け根 | -                    |      |

### 腕

|  ボーン名前   | 必須 |   親ボーン    |  位置の目安  | 親ボーンの存在が必須 | 備考 |
| :------------ | :--- | :------------ | ------------ | -------------------- | ---- |
| leftShoulder  |      | upperChest    |              | No                   |      |
| leftUpperArm  | 必須 | leftShoulder  | 上腕の付け根 | No                   |      |
| leftLowerArm  | 必須 | leftUpperArm  | 肘           | -                    |      |
| leftHand      | 必須 | leftLowerArm  | 手首         | -                    |      |
| rightShoulder |      | upperChest    |              | No                   |      |
| rightUpperArm | 必須 | rightShoulder | 上腕の付け根 | No                   |      |
| rightLowerArm | 必須 | rightUpperArm | 肘           | -                    |      |
| rightHand     | 必須 | rightLowerArm | 手首         | -                    |      |

### 指

|       ボーン名前        | 必須 |        親ボーン         | 位置の目安 | 親ボーンの存在が必須 | 備考 |
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

## ヒューマノイドボーンの親子関係

* ヒューマノイドボーンは親ボーンが決まっています。必須でない親ボーンが存在しない場合は、親ボーンの親ボーンを探してください。
* ヒューマノイドボーンの間にヒューマノイドボーンではないノードが入ることは許容されます(UpperLegとLowerLegの間にヒューマノイドボーンでないノードがあるなど)

hips を root として以下のような親子になります。

* root(ヒューマノイドボーンではない。原点)
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
