# VRMC_vrm

*Version 1.0*

## 목차

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Contributors](#contributors)
- [Status](#status)
- [Dependencies](#dependencies)
- [병용하는 확장](#%EB%B3%91%EC%9A%A9%ED%95%98%EB%8A%94-%ED%99%95%EC%9E%A5)
- [KHR_texture_transform의 제한](#khr_texture_transform%EC%9D%98-%EC%A0%9C%ED%95%9C)
  - [VRM1에서의 KHR_texture_transform 비권장 기능](#vrm1%EC%97%90%EC%84%9C%EC%9D%98-khr_texture_transform-%EB%B9%84%EA%B6%8C%EC%9E%A5-%EA%B8%B0%EB%8A%A5)
- [Overview](#overview)
  - [JSON Schema](#json-schema)
  - [VRMC_vrm의 사양 버전](#vrmc_vrm%EC%9D%98-%EC%82%AC%EC%96%91-%EB%B2%84%EC%A0%84)
  - [형식과 확장자](#%ED%98%95%EC%8B%9D%EA%B3%BC-%ED%99%95%EC%9E%A5%EC%9E%90)
- [glTF Schema Updates](#gltf-schema-updates)
  - [좌표 단위](#%EC%A2%8C%ED%91%9C-%EB%8B%A8%EC%9C%84)
  - [사용하지 않는 항목](#%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80-%EC%95%8A%EB%8A%94-%ED%95%AD%EB%AA%A9)
  - [저장된 TANGENT를 무시해도 무방하다](#%EC%A0%80%EC%9E%A5%EB%90%9C-tangent%EB%A5%BC-%EB%AC%B4%EC%8B%9C%ED%95%B4%EB%8F%84-%EB%AC%B4%EB%B0%A9%ED%95%98%EB%8B%A4)
    - [`meshes[*].primitives[*].attributes.TANGENT`](#meshesprimitivesattributestangent)
    - [`meshes[*].primitives[*].targets.TANGENT`](#meshesprimitivestargetstangent)
  - [`meshes[*].extras.targetNames` 모프 타겟 이름(권장)](#meshesextrastargetnames-%EB%AA%A8%ED%94%84-%ED%83%80%EA%B2%9F-%EC%9D%B4%EB%A6%84%EA%B6%8C%EC%9E%A5)
- [`VRMC_vrm.humanoid` 노드에 휴머노이드 본 할당(필수)](#vrmc_vrmhumanoid-%EB%85%B8%EB%93%9C%EC%97%90-%ED%9C%B4%EB%A8%B8%EB%85%B8%EC%9D%B4%EB%93%9C-%EB%B3%B8-%ED%95%A0%EB%8B%B9%ED%95%84%EC%88%98)
- [`VRMC_vrm.meta` 모델 정보(필수)](#vrmc_vrmmeta-%EB%AA%A8%EB%8D%B8-%EC%A0%95%EB%B3%B4%ED%95%84%EC%88%98)
- [`VRMC_vrm.firstPerson` 1인칭(선택)](#vrmc_vrmfirstperson-1%EC%9D%B8%EC%B9%AD%EC%84%A0%ED%83%9D)
- [Expression, LookAt, SpringBone, Constraints 적용 순서](#expression-lookat-springbone-constraints-%EC%A0%81%EC%9A%A9-%EC%88%9C%EC%84%9C)
- [`VRMC_vrm.expressions` 표정(선택)](#vrmc_vrmexpressions-%ED%91%9C%EC%A0%95%EC%84%A0%ED%83%9D)
- [`VRMC_vrm.lookAt` 시선 제어(선택)](#vrmc_vrmlookat-%EC%8B%9C%EC%84%A0-%EC%A0%9C%EC%96%B4%EC%84%A0%ED%83%9D)
- [Known Implementations](#known-implementations)
- [Resources](#resources)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Contributors

* Shindo Tetsuro (進藤 哲郎)
* Hirose Junichi (廣瀬 淳一)
* Su Po-Chang (蘇 柏彰)
* Obuchi Yutaka (小渕 豊)
* Kado Masataka (角 真宇)

## Status

Complete

## Dependencies

Written against the glTF 2.0 spec.

## 병용하는 확장

VRMC_vrm 확장은 다음 확장들과 함께 사용될 것을 가정하고 있습니다.

* KHR_materials_unlit
* KHR_texture_transform
* KHR_materials_emissive_strength
* VRMC_materials_mtoon
* VRMC_springBone
* VRMC_node_constraint

## KHR_texture_transform의 제한

[KHR_texture_transform](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_texture_transform/README.md)은
모든 머티리얼의 텍스처 [textureInfo](https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/schema/textureInfo.schema.json)에 대해,
개별적으로 `offset`, `rotation`, `scale`, `texCoord`를 지정할 수 있습니다.

glTF 표준 PBR 머티리얼의 경우,

* pbrMetallicRoughness.baseColorTexture (KHR_materials_unlit의 경우는 이것만)
* pbrMetallicRoughness.metallicRoughnessTexture
* normalTexture
* occlusionTexture
* emissiveTexture

입니다.

### VRM1에서의 KHR_texture_transform 비권장 기능

구현에 따라 KHR_texture_transform이 확장하는 항목을 개별적으로 설정하지 못할 수 있습니다.
따라서 다음 항목에 대해서는 사용하지 않을 것을 권장합니다.

* rotation
* texCoord

## Overview

glTF가 씬(Scene)을 표현하는 반면,
VRM은 VR 아바타용 인간형 모델 하나를 표현합니다.

### 모델 공간

VRM에서는 VRM 모델을 구성하는 glTF 씬의 원점으로부터 상대적인 트랜스폼을 관측하는 '모델 공간'을 정의합니다.
이는 VRM 모델을 다루는 애플리케이션 상의 월드 공간과는 구분됩니다.

모델 공간은 [`VRMC_node_constraint`](../VRMC_node_constraint-1.0/README.ja.md) 확장에서 이용됩니다.

> VRM 모델을 애플리케이션 상에서 움직일 때는 Humanoid에서 정의하는 Hips를 움직일 뿐만 아니라,
> glTF 씬의 루트째로 모델을 움직임으로써 모델 공간을 존중하는 것이 기대됩니다.
> 다시 말해, 모델의 루트가 항상 월드 공간의 루트에 머무르는 것 같은 사용은 권장되지 않습니다.

### JSON Schema

```js
{
  "extensionsUsed": [
    "VRMC_vrm"
  ],
  "extensions": {
    "VRMC_vrm": {
      // VRM extension
      "specVersion": "1.0",
      "humanoid": {},
      "meta": {},
      "firstPerson": {},
      "expressions": {},
      "lookAt": {},
    },
    "VRMC_springBone": {},
    "VRMC_node_constraint": {}
  },
  // glTF-2.0
  "materials": [
    "extensions": {
      "VMRC_materials_mtoon": {}
    }
  ],
}
```

* https://github.com/vrm-c/vrm-specification/tree/master/specification/VRMC_vrm-1.0/schema

GLTF-2.0의 JsonSchema

* https://github.com/KhronosGroup/glTF/tree/master/specification/2.0/schema

### VRMC_vrm의 사양 버전

```json
extensions.VRMC_vrm.specVersion = "1.0"
```

### 형식과 확장자

`.glb` 형식으로 저장하고 확장자로 `.vrm`을 사용합니다.

## glTF Schema Updates

### 좌표 단위

glTF의 [coordinate-system-and-units](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#coordinate-system-and-units)를 준수하여 미터(meter) 단위입니다.

### 사용하지 않는 항목

다음 항목은 사용하지 않습니다.

* animations
* cameras

### 저장된 TANGENT를 무시해도 무방하다

TANGENT를 올바르게 다루는 것이 기술적으로 어렵기 때문에 내보내지 않거나, 읽지 않고 계산하는 것을 허용합니다.

#### `meshes[*].primitives[*].attributes.TANGENT`

* import: MikkTSpace 알고리즘으로 계산해 주십시오.

https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md#meshes

> Implementation note: When tangents are not specified, client implementations should calculate tangents using default MikkTSpace algorithms. For best results, the mesh triangles should also be processed using default MikkTSpace algorithms.

* export: MikkTSpace 알고리즘으로 계산할 것을 기대하고 export하지 않는 것을 권장합니다.

#### `meshes[*].primitives[*].targets.TANGENT`

* morphTarget에서 tangent가 애니메이션되는 것은 권장하지 않습니다.
* export: 출력하지 않는 것을 권장합니다.
* import: 무시하는 것을 권장합니다.

### `meshes[*].extras.targetNames` 모프 타겟 이름(권장)

`meshes[*].primitives[*].targets.name`이 없으므로 대체하여 `meshes[*].extras.targetNames`에 저장합니다.

* https://github.com/KhronosGroup/glTF/issues/1036

## `VRMC_vrm.humanoid` 노드에 휴머노이드 본 할당(필수)

인간형 모델을 정의하기 위해 인체의 부위(휴머노이드 본)를 glTF.Node에 할당합니다.

별도 문서에서 사양을 설명합니다.

[./humanoid.md](./humanoid.md)

## `VRMC_vrm.meta` 모델 정보(필수)

별도 문서에서 사양을 설명합니다.

[./meta.md](./meta.md)

## `VRMC_vrm.firstPerson` 1인칭(선택)

VRM은 VR을 가정한 1인칭 시점의 설정을 정의하고 있습니다.

별도 문서에서 사양을 설명합니다.

[./firstPerson.md](./firstPerson.md)

## Expression, LookAt, SpringBone, Constraints 적용 순서 

* VRMC_vrm.lookAt
* VRMC_vrm.expression
* VRMC_node_constraint
* VRMC_springBone

은 Node, Mesh에 변경이 있어 실행 순서의 영향을 받습니다.
권장되는 업데이트 적용 순서는 다음과 같습니다.

1. 휴머노이드 본을 해결
2. 머리 위치가 결정되므로 LookAt을 해결
  * Bone 타입 => leftEye, rightEye 본을 회전
  * Expression 타입 => 다음 항목
3. ExpressionUpdate
  * 희로애락 컨트롤러 등 외부 입력 => Expression 가중치(weight)를 설정
  * LipSync => Expression 가중치를 설정
  * AutoBlink => Expression 가중치를 설정
  * Expression 타입의 LookAt => Expression 가중치를 설정
4. Expression을 Apply한다
5. 제약(Constraint)을 해결
6. SpringBone을 해결

## `VRMC_vrm.expressions` 표정(선택)

VRM은 휴머노이드용으로 Expression을 정의하고 있습니다.

> VRM-0 사양에서 사용하던 BlendShape라는 단어는 MorphTarget과 같은 것을 가리키지만 의미가 다르므로, BlendShape에서 Expression으로 이름을 변경했습니다.

별도 문서에서 사양을 설명합니다.

[./expressions.md](./expressions.md)

## `VRMC_vrm.lookAt` 시선 제어(선택)

VRM은 휴머노이드용으로 시선 제어를 정의하고 있습니다.

별도 문서에서 사양을 설명합니다.

[./lookAt.md](./lookAt.md)

## Known Implementations

* https://vrm.dev/vrm_applications/

## Resources

* https://vrm.dev/
