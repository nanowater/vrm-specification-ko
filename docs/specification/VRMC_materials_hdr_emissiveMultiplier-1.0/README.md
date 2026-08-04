# VRMC_materials_hdr_emissiveMultiplier

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

## Contributors

- 카쿠 마우
- 오부치 유타카
- 신도 테츠로

## Status

Archived

다음으로 대체됨 [KHR_materials_emissive_strength](https://github.com/KhronosGroup/glTF/blob/main/extensions/2.0/Khronos/KHR_materials_emissive_strength/README.md)

## Dependencies

glTF 2.0 사양을 바탕으로 작성되었습니다.

## Overview

이 확장은 매테리얼의 EmissiveFactor 값에 곱하여 이를 덮어씁니다.
1보다 큰 값의 EmissiveFactor를 가질 수 있습니다.

매테리얼의 `extensions`에 정의합니다.

```json
{
  "materials": [
    {
      "name": "MyPBRMaterial",
      // emission
      "emissiveTexture": {},
      "emissiveFactor": [1, 1, 1],
      // extension
      "extensions": {
        "VRMC_materials_hdr_emissiveMultiplier": {
          "emissiveMultiplier": 2.0
        }
      }
    }
  ]
}
```

## Defined Properties

|                    | 타입   | 설명                            | 필수 |
| ------------------ | ------ | ------------------------------- | :--- |
| emissiveMultiplier | number | A multiplier for emissiveFactor | ✅   |

대상 material의 material.emissiveFactor를 emissiveMultiplier로 곱한 값으로 덮어씁니다.
이 값은 리니어(linear)입니다.

## export 시의 변환 예

다음과 같이 변환해 주십시오.

```js
// linear color space
let hdr_emissive_factor = [r, g, b];

let max_component = r;
if (g > max_component) {
  max_component = g;
}
if (b > max_component) {
  max_component = b;
}

if (max_component > 1) {
  // linear color space
  let emissiveFactor = [
    r / max_component,
    g / max_component,
    b / max_component,
  ];
  let emissiveMultiplier = max_component;
  // VRMC_materials_hdr_emissiveMultiplier를 통해 1을 초과하는 emissive factor 값을 나타냅니다.
} else {
  // linear color space
  let emissiveFactor = [r, g, b];
  let emissiveMultiplier = null;
  // VRMC_materials_hdr_emissiveMultiplier 확장은 불필요합니다.
}
```
