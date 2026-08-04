# VRM 사양

[glTF-2.0](https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md)의 바이너리 형식 glb를 기반으로 한, VR용 모델 포맷입니다.
VRM 사양의 저장소는 여기: [VRM specification](https://github.com/vrm-c/vrm-specification). 

# 업데이트 내역

* 20181109: JsonSchema의 `Vector3` 타입이 잘못하여 `array`로 되어있던 부분 수정

```js
{
    "x": {
        "type": "number"
    },
    "y": {
        "type": "number"
    },
    "z": {
        "type": "number"
    }
}
```

# 확장자

`.vrm`을 사용합니다.
gltf의 바이너리 형식 `.glb`와 호환되므로, 확장자를 `.glb`로 변경하면 GLTF 지원 애플리케이션에서 읽어들일 수 있습니다(VRM 자체의 추가 정보는 사라집니다).

# Json 확장

GLB의 JSON 부분에 `VRM Extension`으로서 확장하고 있습니다.

```js
{
  "extensionsUsed": {
    "VRM"
  },
  "extensions": {
    "VRM": {
      // VRM의 확장 정보
    }
  },
  // 일반적인 GLTF-2.0 정보
}
```

사양의 JsonSchema를 작성했습니다.

* https://github.com/vrm-c/vrm-specification/tree/master/specification/0.0/schema

GLTF-2.0의 JsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

# VRM 확장: VRM 버전 등

* `/extensions/VRM/exporterVersion`은 v0.36부터

```js
{
    "title": "vrm",
    "description": "VRM extension is for 3d humanoid avatars (and models) in VR applications.",
    "type": "object",
    "properties": {
        "exporterVersion": {
            "description": "Version of exporter that vrm created. UniVRM-0.42",
            "type": "string"
        },
        "meta": {
            "$ref": "vrm.meta.schema.json"
        },
        "humanoid": {
            "$ref": "vrm.humanoid.schema.json"
        },
        "firstPerson": {
            "$ref": "vrm.firstperson.schema.json"
        },
        "blendShapeMaster": {
            "$ref": "vrm.blendshape.schema.json"
        },
        "secondaryAnimation": {
            "$ref": "vrm.secondaryanimation.schema.json"
        },
        "materialProperties": {
            "type": "array",
            "items": {
                "$ref": "vrm.material.schema.json"
            }
        }
    }
}
```

# 저장 가능한 요소

인간형 모델 한 체분의 정보를 저장합니다.

## GLTF-2.0: Texture

* GLTF-2.0의 `/textures/`

VRM 확장은 없습니다.

## GLTF-2.0: Material(json.extensions.VRM.materialProperties)

* GLTF-2.0의 `/materials/`

GLTF의 매테리얼로 폴백(fallback)된 정보를 저장하고 있습니다(확장자를 GLB로 변경했을 경우에 사용됨).

## VRM 확장: `/extensions/VRM/materialProperties`

VRM 독자적인 매테리얼 정보를 저장하고 있습니다.
현재 Unity에 필요한 항목을 저장하고 있습니다.
선택 가능한 Shader는 [VRM이 제공하는 셰이더](#vrmshader)를 참조하십시오.

* https://github.com/vrm-c/UniVRM/blob/master/specification/0.0/schema/vrm.material.schema.json

```js
{
    "title": "vrm.material",
    "type": "object",
    "properties": {
        "name": {
            "type": "string"
        },
        "shader": {
            "type": "string"
        },
        "renderQueue": {
            "type": "integer"
        },
        "floatProperties": {
            "type": "object"
        },
        "vectorProperties": {
            "type": "object"
        },
        "textureProperties": {
            "type": "object"
        },
        "keywordMap": {
            "type": "object"
        },
        "tagMap": {
            "type": "object"
        }
    }
}
```

## GLTF-2.0: Mesh

* GLTF-2.0의 `/meshes/`

VRM 확장은 없습니다.

### 버텍스 어트리뷰트

* GLTF-2.0의 `/meshes/*/primitives/*/attributes`

  * TANGENT (vec4) // v0.42부터 저장을 중단하고, import 시에 normal과 uv로부터 계산하도록 하고 있습니다.

### 모프 타겟 정보

* `/meshes/*/primitives/*/extras/targetNames`

에 MorphTarget의 명칭을 기록하고 있습니다.

## GLTF-2.0: 스키닝 정보

* GLTF-2.0의 `/skins/`

VRM 확장은 없습니다.

## GLTF-2.0: Node

* GLTF-2.0의 `/nodes/`

VRM 확장은 없습니다.

* node
  * name
  * position(vec3)
  * rotation(quaternion)
  * scale(vec3)

# 저장하는 값에 대한 규약

## GLTF2의 규약

GLTF2의 규약을 준수합니다.
특히 중요한 항목입니다.

* 미터 단위
* 오른손 Y-UP 좌표계[^OpenGLCoord]

[^OpenGLCoord]: OpenGL 좌표계. +X가 오른쪽, +Y가 위, +Z가 앞쪽입니다.

## VRM의 규약

인간형 모델에 특화하여 호환성을 높이기 위해, 이하의 제약을 부여합니다.

* 모델은 원점에 위치한다
* 모델은 -Z 방향을 향한다[^OpenGLCoord]
* 모델의 계층 구조는 Y-UP[^ZUP]
* 모델의 메시(Mesh)는 Y-UP[^ZUP]
* 모델의 계층 구조는 T-Pose
* 모델의 메시(Mesh)는 T-Pose
* 본(Bone)에 회전을 넣지 않는다
* 본(Bone)에 스케일을 넣지 않는다
* 헤드 본은 정면을 향하고 있다[^LookAt]

[^ZUP]: Blender나 3ds Max 등의 Z-UP 모델러에서 유래한 모델로, 계층 중간에 x축 -90도 회전을 넣어 Z-UP이 중첩되어 있는 경우가 있습니다.
[^LookAt]: 시선 제어는 T-Pose 시의 헤드 방향을 기준으로 목표물의 방향을 계산합니다.

<a name="vrmshader"></a>

# VRM이 제공하는 셰이더

셀(Cel) 느낌의 캐릭터 모델 운용을 상정하여 다음의 셰이더를 준비하고 있습니다.

## Unlit계

라이팅・셰이딩을 하지 않고 텍스처 색을 그대로 표시합니다.
반투명 처리에 따라 4종류를 준비하고 있습니다.

* UnlitTexture(불투명)
* UnlitCutout(투명도가 임곗값 이하인 부분을 투명하게 처리)
* UnlitTransparent(알파 블렌드. ZWrite 안 함)[^Transparent]
* UnlitTransparentZWrite(알파 블렌드. ZWrite 함)[^TransparentZWrite]

[^Transparent]: 연기나 볼의 홍조 등 실체가 없는 오브젝트용입니다.
[^TransparentZWrite]: 반투명 의상이나, 머리카락 끝이 반투명한 등 실체가 있는 오브젝트용입니다.

## MToon

셀 셰이딩, 윤곽선에 대응하는 셰이더.
Unlit보다 더 세밀하게 설정할 수 있습니다.

# VRM 확장: 모델의 본 매핑(json.extensions.VRM.humanoid)

Node와 Humanoid에서 정의되는 표준 본의 대응표입니다.

```js
{
    "title": "vrm.humanoid.bone",
    "type": "object",
    "properties": {
        "bone": {
            "description": "Human bone name.",
            "type": "string",
            "enum": ["hips","leftUpperLeg","rightUpperLeg","leftLowerLeg","rightLowerLeg","leftFoot","rightFoot","spine","chest","neck","head","leftShoulder","rightShoulder","leftUpperArm","rightUpperArm","leftLowerArm","rightLowerArm","leftHand","rightHand","leftToes","rightToes","leftEye","rightEye","jaw","leftThumbProximal","leftThumbIntermediate","leftThumbDistal","leftIndexProximal","leftIndexIntermediate","leftIndexDistal","leftMiddleProximal","leftMiddleIntermediate","leftMiddleDistal","leftRingProximal","leftRingIntermediate","leftRingDistal","leftLittleProximal","leftLittleIntermediate","leftLittleDistal","rightThumbProximal","rightThumbIntermediate","rightThumbDistal","rightIndexProximal","rightIndexIntermediate","rightIndexDistal","rightMiddleProximal","rightMiddleIntermediate","rightMiddleDistal","rightRingProximal","rightRingIntermediate","rightRingDistal","rightLittleProximal","rightLittleIntermediate","rightLittleDistal","upperChest"]
        },
        "node": {
            "description": "Reference node index",
            "type": "integer"
        },
        "useDefaultValues": {
            "description": "Unity's HumanLimit.useDefaultValues",
            "type": "boolean"
        },
        "min": {
            "description": "Unity's HumanLimit.min",
            "type": "array"
        },
        "max": {
            "description": "Unity's HumanLimit.max",
            "type": "array"
        },
        "center": {
            "description": "Unity's HumanLimit.center",
            "type": "array"
        },
        "axisLength": {
            "description": "Unity's HumanLimit.axisLength",
            "type": "number"
        }
    }
}
```

## 정의하고 있는 본

|본 이름                |필수・옵션      |
|:---------------------|:--------------|
|neck                  |필수           |
|head                  |필수           |
|left/right Eye        |옵션           |
|jaw                   |옵션           |
|hips                  |필수           |
|spine                 |필수           |
|chest                 |필수           |
|upperChest            |옵션           |
|left/right Shoulder   |옵션           |
|left/right UpperArm   |필수           |
|left/right LowerArm   |필수           |
|left/right Hand       |필수           |
|left/right UpperLeg   |필수           |
|left/right LowerLeg   |필수           |
|left/right Foot       |필수           |
|left/right Toe        |옵션           |  
|left/right Thumb Proximal, Intermediate, Distal |옵션|
|left/right Index Proximal, Intermediate, Distal |옵션|
|left/right Middle Proximal, Intermediate, Distal|옵션|
|left/right Ring Proximal, Intermediate, Distal  |옵션|
|left/right Little Proximal, Intermediate, Distal|옵션|

# VRM 확장: 모델 정보(json.extensions.VRM.meta)

```js
{
    "title": "vrm.meta",
    "type": "object",
    "properties": {
        "title": {
            "description": "Title of VRM model",
            "type": "string"
        },
        "version": {
            "description": "Version of VRM model",
            "type": "string"
        },
        "author": {
            "description": "Author of VRM model",
            "type": "string"
        },
        "contactInformation": {
            "description": "Contact Information of VRM model author",
            "type": "string"
        },
        "reference": {
            "description": "Reference of VRM model",
            "type": "string"
        },
        "texture": {
            "description": "Thumbnail of VRM model",
            "type": "integer"
        },
        "allowedUserName": {
            "description": "A person who can perform with this avatar",
            "type": "string",
            "enum": ["OnlyAuthor","ExplicitlyLicensedPerson","Everyone"]
        },
        "violentUssageName": {
            "description": "Permission to perform violent acts with this avatar",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "sexualUssageName": {
            "description": "Permission to perform sexual acts with this avatar",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "commercialUssageName": {
            "description": "For commercial use",
            "type": "string",
            "enum": ["Disallow","Allow"]
        },
        "otherPermissionUrl": {
            "description": "If there are any conditions not mentioned above, put the URL link of the license document here.",
            "type": "string"
        },
        "licenseName": {
            "description": "License type",
            "type": "string",
            "enum": ["Redistribution_Prohibited","CC0","CC_BY","CC_BY_NC","CC_BY_SA","CC_BY_NC_SA","CC_BY_ND","CC_BY_NC_ND","Other"]
        },
        "otherLicenseUrl": {
            "description": "If “Other” is selected, put the URL link of the license document here.",
            "type": "string"
        }
    }
}
```

## 정보

### 타이틀(Title)

아바타 모델의 이름을 설정합니다.

### 제작자(Author)

모델의 제작자 이름을 기술합니다.

### 연락처(Contact Information)

모델 제작자의 연락처를 기술합니다.

> 이 프로퍼티는 사용자가 어떠한 이유로 제작자에게 연락을 취하고 싶을 경우를 대비하여 소셜 계정 정보나 웹사이트 등 모델 제작자의 연락처 정보를 표시하기 위해 사용될 것을 의도하고 있습니다.
> 전화번호나 주소 등 대중에게 공개될 것을 의도하지 않는 개인 정보는 포함하지 않도록 해주십시오.

### 참조(Reference)

어떠한 '원작'에 해당하는 것이 있는 경우는 참조 URL 등을 기술합니다.

### 썸네일(Thumbnail)

아바타 모델의 썸네일을 등록합니다. 2048x2048 정도의 해상도 텍스처를 권장합니다. meta 정보 내에서는 texture 번호를 지정합니다.

### 버전

모델의 제작 버전입니다.

## 사용 허가・라이선스 정보

License

### 아바타의 인격에 관한 허가 범위

Personation / Characterization Permission

#### 아바타에 인격을 부여하는 것의 허가 범위(json.extensions.VRM.meta.allowedUserName)

A person who can perform with this avatar

* 아바타를 조작하는 것은 아바타 제작자에게만 허용된다(Only Author)
* 명확하게 허가된 사람만(Explictly Licensed Person)
* 모두에게 허가(Everyone)

#### 이 아바타를 사용하여 폭력 표현을 연기하는 것의 허가(json.extensions.VRM.meta.violentUssageName)

Violent acts using this avatar

* 불허(Disallow)
* 허가(Allow)

#### 이 아바타를 사용하여 성적 표현을 연기하는 것의 허가(json.extensions.VRM.meta.sexualUssageName)

Sexuality acts using this avatar

* 불허(Disallow)
* 허가(Allow)

#### 상업적 이용의 허가(json.extensions.VRM.meta.commercialUssageName)

For commercial use

* 불허(Disallow)
* 허가(Allow)

#### 그 외 라이선스 조건(json.extensions.VRM.meta.otherPermissionUrl)

Other License Url

상기 허가 조건 이외의 라이선스 조건이 있는 경우는 그 라이선스 문서의 URL을 기술

### 재배포・수정에 관한 허가 범위

Redistribution / Modifications License

#### 라이선스 타입(json.extensions.VRM.meta.licenseName)

License Type

* 재배포 금지(Redistribution Prohibited)
* [저작권 포기(CC0)](https://creativecommons.org/publicdomain/zero/1.0/deed.ko)
* [Creative Commons CC BY 라이선스(CC_BY)](https://creativecommons.org/licenses/by/4.0/deed.ko)
* [Creative Commons CC BY NC 라이선스(CC_BY_NC)](https://creativecommons.org/licenses/by-nc/4.0/deed.ko)
* [Creative Commons CC BY SA 라이선스(CC_BY_SA)](https://creativecommons.org/licenses/by-sa/4.0/deed.ko)
* [Creative Commons CC BY NC SA 라이선스(CC_BY_NC_SA)](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.ko)
* [Creative Commons CC BY ND 라이선스(CC_BY_ND)](https://creativecommons.org/licenses/by-nd/4.0/deed.ko)
* [Creative Commons CC BY NC ND 라이선스(CC_BY_NC_ND)](https://creativecommons.org/licenses/by-nc-nd/4.0/deed.ko)
* 기타(Other)

#### 그 외 라이선스 조건(json.extensions.VRM.meta.otherLicenseUrl)

Other License Url

상기 허가 조건 이외의 라이선스 조건이 있는 경우는 그 라이선스 문서의 URL을 기술

# VRM 확장: 모프 설정(json.extensions.VRM.blendShapeMaster)

BlendShape를 그룹화하는 BlendShapeGroup의 배열을 설정합니다.

```js
{
    "title": "vrm.blendshape",
    "type": "object",
    "properties": {
        "blendShapeGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.group.schema.json"
            }
        }
    }
}
```

## 블렌드 셰이프 그룹(json.extensions.VRM.blendShapeMaster.blendShapeGroups)

```js
{
    "title": "vrm.blendshape.group",
    "type": "object",
    "properties": {
        "name": {
            "description": "Expression name",
            "type": "string"
        },
        "presetName": {
            "description": "Predefined Expression name",
            "type": "string",
            "enum": ["Neutral","A","I","U","E","O","Blink","Joy","Angry","Sorrow","Fun","LookUp","LookDown","LookLeft","LookRight","Blink_L","Blink_R"]
        },
        "binds": {
            "description": "Low level blendshape references.",
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.bind.schema.json"
            }
        },
        "materialValues": {
            "description": "Material animation references.",
            "type": "array",
            "items": {
                "$ref": "vrm.blendshape.materialbind.schema.json"
            }
        }
    }
}
```

```js
{
    "title": "vrm.blendshape.bind",
    "type": "object",
    "properties": {
        "mesh": {
            "type": "integer"
        },
        "index": {
            "type": "integer"
        },
        "weight": {
            "type": "number"
        }
    }
}
```

```js
{
    "title": "vrm.blendshape.materialbind",
    "type": "object",
    "properties": {
        "materialName": {
            "type": "string"
        },
        "propertyName": {
            "type": "string"
        },
        "targetValue": {
            "type": "array",
            "items": {
                "type": "number"
            }
        }
    }
}
```

### 이름

이름입니다. 사전 정의 이름과 동일(대문자)하게 설정하는 것을 권장합니다.

### 사전 정의 이름

대기 상태의 표정

* Neutral

립싱크

* A
* I
* U
* E
* O

눈 깜빡임

* Blink
* Blink_L
* Blink_R

희로애락

* Fun
* Angry
* Sorrow
* Joy

시선 제어

* LookUp
* LookDown
* LookLeft
* LookRight

기타

* Unknown

### 블렌드 셰이프 이름의 식별명

시스템에서 블렌드 셰이프를 고유하게 인식하는 문자열 ID를 이하의 로직으로 결정합니다.

```
// 의사 코드
function GetID(preset, name)
{
  if (Preset != BlendShapePreset.Unknown)
  {
      return preset.ToString().ToUpper();
  }
  else
  {
      return name.ToUpper();
  }
}
```

* 블렌드 셰이프 ID가 고유해지도록 Preset과 Name을 설정한다.

# VRM 확장: 1인칭 설정(json.extensions.VRM.firstPerson)

```js
{
    "title": "vrm.firstperson",
    "type": "object",
    "properties": {
        "firstPersonBone": {
            "description": "The bone whose rendering should be turned off in first-person view. Usually Head is specified.",
            "type": "integer"
        },
        "firstPersonBoneOffset": {
            "description": "The target position of the VR headset in first-person view. It is assumed that an offset from the head bone to the VR headset is added.",
            "type": "object",
            "properties": {
                "x": {
                    "type": "number"
                },
                "y": {
                    "type": "number"
                },
                "z": {
                    "type": "number"
                }
            }
        },
        "meshAnnotations": {
            "description": "Switch display \/ undisplay for each mesh in first-person view or the others.",
            "type": "array",
            "items": {
                "$ref": "vrm.firstperson.meshannotation.schema.json"
            }
        },
        "lookAtTypeName": {
            "description": "Eye controller mode.",
            "type": "string",
            "enum": ["Bone","BlendShape"]
        },
        "lookAtHorizontalInner": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtHorizontalOuter": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtVerticalDown": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        },
        "lookAtVerticalUp": {
            "$ref": "vrm.firstperson.degreemap.schema.json"
        }
    }
}
```

1인칭 시점의 아바타를 그릴 경우, 자신의 모델 머릿속이 보여버리는 문제가 발생합니다[^firstperson].
이에 대응하기 위해, 1인칭 시의 표시 상태를 지정할 수 있습니다.

[^firstperson]: 백페이스 컬링이나 근접 평면으로 어느 정도 대처할 수 있지만, 입안이 만들어져 있는 모델의 잇몸이 의도치 않게 보이는 등 불충분한 경우가 있습니다.

## firstPersonBone(json.extensions.VRM.firstPerson.firstPersonBone)

1인칭 시에 렌더링을 전환해야 할 본을 지정합니다. 일반적으로 `Head`입니다.

## firstPersonBoneOffset(json.extensions.VRM.firstPerson.firstPersonBoneOffset)

1인칭 시의 헤드셋 목표 위치.
머리 본에서 헤드셋으로의 오프셋을 더하는 것을 상정하고 있습니다.

## meshAnnotations(json.extensions.VRM.firstPerson.meshAnnotations)

각 메시에 대해 1인칭 시점과 그 외의 경우에 표시・비표시를 전환할 수 있습니다.
이하의 설정이 있습니다.

* Auto: firstPersonBone과 그 자손에 대해 본 Weight를 갖는 폴리곤을 자동으로 비표시합니다.[^firstPersonAuto]
* FirstPersonOnly: 1인칭 시에만 표시
* ThirdPersonOnly: 3인칭 시에만 표시(머리 등 1인칭 시에 비표시할 메시에 지정합니다)
* Both: 딱히 표시 전환을 하지 않음

[^firstPersonAuto]: 실행 시에 자동으로 비표시 부분을 삭제한 모델을 생성합니다.

## 시선 설정

타깃 방향을 향하도록 캐릭터의 시선을 제어합니다.

### 시선 타입(json.extensions.VRM.firstPerson.lookAtTypeName)

* Bone: 본으로 시선을 조작합니다.
* BlendShape: BlendShape로 시선을 조작합니다. BlendShapePreset.LookUp, LookDown, LookLeft, LookRight를 사용합니다.

### 각도 조정

머리와 목표물의 각도 차이를 눈 본에 적용할 경우의 각도를 조정합니다.

#### json.extensions.VRM.firstPerson.lookAtHorizontalInner

#### json.extensions.VRM.firstPerson.lookAtHorizontalOuter

#### json.extensions.VRM.firstPerson.lookAtVerticalDown

#### json.extensions.VRM.firstPerson.lookAtVerticalUp

# VRM 확장: 흔들림 설정(json.extensions.VRM.secondaryAnimation)

꼬리나 머리카락 등 끈 모양 오브젝트의 자동 애니메이션 설정입니다.

## 흔들리는 본(secondaryAnimation.boneGroups)

```js
{
    "title": "vrm.secondaryanimation",
    "type": "object",
    "properties": {
        "boneGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.secondaryanimation.spring.schema.json"
            }
        },
        "colliderGroups": {
            "type": "array",
            "items": {
                "$ref": "vrm.secondaryanimation.collidergroup.schema.json"
            }
        }
    }
}
```

```js
{
    "title": "vrm.secondaryanimation.spring",
    "type": "object",
    "properties": {
        "comment": {
            "description": "Annotation comment",
            "type": "string"
        },
        "stiffiness": {
            "description": "The resilience of the swaying object (the power of returning to the initial pose).",
            "type": "number"
        },
        "gravityPower": {
            "description": "The strength of gravity.",
            "type": "number"
        },
        "gravityDir": {
            "description": "The direction of gravity. Set (0, -1, 0) for simulating the gravity. Set (1, 0, 0) for simulating the wind.",
            "type": "object",
            "properties": {
                "x": {
                    "type": "number"
                },
                "y": {
                    "type": "number"
                },
                "z": {
                    "type": "number"
                }
            }
        },
        "dragForce": {
            "description": "The resistance (deceleration) of automatic animation.",
            "type": "number"
        },
        "center": {
            "description": "The reference point of a swaying object can be set at any location except the origin. When implementing UI moving with warp, the parent node to move with warp can be specified if you don't want to make the object swaying with warp movement.",
            "type": "integer"
        },
        "hitRadius": {
            "description": "The radius of the sphere used for the collision detection with colliders.",
            "type": "number"
        },
        "bones": {
            "description": "Specify the node index of the root bone of the swaying object.",
            "type": "array",
            "items": {
                "type": "integer"
            }
        },
        "colliderGroups": {
            "description": "Specify the index of the collider group for collisions with swaying objects.",
            "type": "array",
            "items": {
                "type": "integer"
            }
        }
    }
}
```

### 흔들리는 본의 뿌리 본(json.extensions.VRM.secondaryAnimation.boneGroups[0].bones)

흔들림이 시작되는 뿌리 본의 노드 인덱스를 지정합니다.

### 흔들리는 본과 충돌하는 판정(json.extensions.VRM.secondaryAnimation.boneGroups[0].colliderGroups)

흔들림에 대한 충돌 판정 그룹의 인덱스를 지정합니다.

### 파라미터

#### center(json.extensions.VRM.secondaryAnimation.boneGroups[0].center)

world 원점 외에, 흔들림의 기준점을 설정할 수 있습니다.
워프로 이동하는 UI를 구현했을 경우에, 워프 이동 시 흔들림을 원하지 않을 때 워프로 이동하는 부모 노드를 지정할 수 있습니다.

#### dragForce(json.extensions.VRM.secondaryAnimation.boneGroups[0].dragForce)

자동 애니메이션의 저항(감속)입니다.

#### gravityDir(json.extensions.VRM.secondaryAnimation.boneGroups[0].gravityDir)

중력의 방향입니다. (0, -1, 0)으로 설정하면 중력으로, (1, 0, 0)으로 설정하면 바람처럼 작용합니다.

#### gravityPower(json.extensions.VRM.secondaryAnimation.boneGroups[0].gravityPower)

중력의 세기입니다.

#### hitRadius(json.extensions.VRM.secondaryAnimation.boneGroups[0].hitRadius)

Collider와의 충돌 판정 반경입니다.

#### stiffness(json.extensions.VRM.secondaryAnimation.boneGroups[0].stiffiness)

흔들림의 복원력(초기 자세로 돌아가려는 힘)입니다.

## 흔들림 충돌 판정 설정(json.extensions.VRM.secondaryAnimation.colliderGroups)

흔들리는 부위와 충돌하는 구(Sphere)를 설정합니다.

```js
{
    "title": "vrm.secondaryanimation.collidergroup",
    "type": "object",
    "properties": {
        "node": {
            "description": "The node of the collider group for setting up collision detections.",
            "type": "integer"
        },
        "colliders": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "offset": {
                        "description": "The local coordinate from the node of the collider group.",
                        "type": "object",
                        "properties": {
                            "x": {
                                "type": "number"
                            },
                            "y": {
                                "type": "number"
                            },
                            "z": {
                                "type": "number"
                            }
                        }
                    },
                    "radius": {
                        "description": "The radius of the collider.",
                        "type": "number"
                    }
                }
            }
        }
    }
}
```

### 노드(json.extensions.VRM.secondaryAnimation.colliderGroups[0].node)

충돌 판정을 설치할 노드입니다.

### 로컬 좌표(json.extensions.VRM.secondaryAnimation.colliderGroups[0].colliders[1].offset)

충돌 판정의 노드로부터의 로컬 좌표입니다.

### 반경(json.extensions.VRM.secondaryAnimation.colliderGroups[0].colliders[1].radius)

충돌 판정의 반경입니다.
