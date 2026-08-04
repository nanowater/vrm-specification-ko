# **⚠ 이 문서는 오래되었습니다 (This document is outdated).**

!!! warning "🤖 AI 자동 번역 문서"

    이 문서는 AI로 자동 번역된 문서입니다. 아직 검수가 완료되지 않았으므로 **오역이나 부정확한 표현이 포함될 수 있습니다**.

    정확한 내용은 [원본 vrm-specification 문서](https://github.com/vrm-c/vrm-specification)와 비교하여 확인해 주세요.

- 0.0은 unity 셰이더의 프로퍼티 이름에서 파생되었습니다
- 1.0은 json.key입니다

| 0.0                       | 1.0draft                                                                                  | new color space*1 or type       | memo                    |
| ------------------------- | ----------------------------------------------------------------------------------------- | ------------------------------- | ----------------------- |
| _MToonVersion             | materials.i.extensions.VRMC_materials_mtoon.version                                       | int->str                        |                         |
| _BlendMode                | materials.i.alphaMode *3                                                                  | int->Enum(str)                  |                         |
|                           | materials.i.extensions.VRMC_materials_mtoon.transparentWithZWrite                         | bool                            |                         |
| _CullMode                 | materials.i.doubleSided                                                                   | int->bool                       | none=>true, back=>false |
| renderQueue               | materials.i.extensions.VRMC_materials_mtoon.renderQueueOffsetNumber *2                    | int                             |                         |
| _Color                    | materials.i.pbrMetallicRoughness.baseColorFactor                                          | Linear                          |                         |
| _MainTex                  | materials.i.pbrMetallicRoughness.baseColorTexture.index                                   | sRGB                            |                         |
| _ShadeColor               | materials.i.extensions.VRMC_materials_mtoon.shadeFactor                                   | Linear                          |                         |
| _ShadeTexture             | materials.i.extensions.VRMC_materials_mtoon.shadeMultiplyTexture                          | sRGB                            |                         |
| _Cutoff                   | materials.i.alphaCutoff                                                                   | mixed decimal                   |                         |
| _ShadeShift               | materials.i.extensions.VRMC_materials_mtoon.shadingShiftFactor                            | mixed decimal                   |                         |
| _ShadeToony               | materials.i.extensions.VRMC_materials_mtoon.shadingToonyFactor                            | mixed decimal                   |                         |
| _ReceiveShadowRate        | materials.i.extensions.VRMC_materials_mtoon.shadowReceiveMultiplierFactor                 | mixed decimal                   |                         |
| _ReceiveShadowTexture     | ~~shadowReceiveMultiplierMultiplyTexture~~                                                | Linear                          | Will be deleted         |
| _ShadingGradeRate         | ~~litAndShadeMixingMultiplierFactor~~                                                     | mixed decimal                   | Will be deleted         |
| _ShadingGradeTexture      | ~~litAndShadeMixingMultiplierMultiplyTexture~~                                            | Linear                          | Will be deleted         |
| _LightColorAttenuation    | ~~lightColorAttenuationFactor~~                                                           | mixed decimal                   | Will be deleted         |
| _IndirectLightIntensity   | materials.i.extensions.VRMC_materials_mtoon.giIntensityFactor                             | mixed decimal                   |                         |
| _BumpMap                  | materials.i.extensions.VRMC_materials_mtoon.normalTexture.index                           | Linear                          |                         |
| _BumpScale                | materials.i.extensions.VRMC_materials_mtoon.normalTexture.scale                           | mixed decimal                   |                         |
| _EmissionColor            | materials.i.emissiveFactor                                                                | Linear                          |                         |
| _EmissionMap              | materials.i.emissiveTexture.index                                                         | sRGB                            |                         |
| _SphereAdd                | materials.i.extensions.additiveTexture                                                    | sRGB                            |                         |
| _RimColor                 | materials.i.extensions.rimFactor                                                          | Linear                          |                         |
| _RimTexture               | materials.i.extensions.rimMultiplyTexture                                                 | sRGB                            |                         |
| _RimLightingMix           | materials.i.extensions.rimLightingMixFactor                                               | mixed decimal                   |                         |
| _RimFresnelPower          | materials.i.extensions.rimFresnelPowerFactor                                              | mixed decimal                   |                         |
| _RimLift                  | materials.i.extensions.rimLiftFactor                                                      | mixed decimal                   |                         |
| _OutlineWidthMode         | materials.i.extensions.outlineWidthMode                                                   | int->Enum(str)                  |                         |
| _OutlineColor             | materials.i.extensions.outlineWidthFactor                                                 | Linear                          |                         |
| _OutlineWidthTexture      | materials.i.extensions.outlineWidthMultiplyTexture                                        | Linear                          |                         |
| _OutlineScaledMaxDistance | materials.i.extensions.outlineScaledMaxDistanceFactor                                     | mixed decimal                   |                         |
| _OutlineLightingMix       | materials.i.extensions.outlineColorMode                                                   | int->Enum(str)                  |                         |
| _OutlineWidth             | materials.i.extensions.outlineFactor                                                      | mixed decimal                   |                         |
| _OutlineLightingMix       | materials.i.extensions.outlineLightingMixFactor                                           | mixed decimal                   |                         |
| _MainTex                  | materials.i.pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform.offset | _Maintex[0:2] ->2 mixed decimal |                         |
| _MainTex                  | materials.i.pbrMetallicRoughness.baseColorTexture.extensions.KHR_texture_transform.scale  | _Maintex[2:4] ->2 mixed decimal |                         |
| _UvAnimMaskTexture        | materials.i.extensions.uvAnimationMaskTexture                                             | Linear                          |                         |
| _UvAnimScrollX            | materials.i.extensions.uvAnimationScrollXSpeedFactor                                      | mixed decimal                   |                         |
| _UvAnimScrollY            | materials.i.extensions.uvAnimationScrollYSpeedFactor                                      | mixed decimal                   |                         |
| _UvAnimRotation           | materials.i.extensions.uvAnimationRotationSpeedFactor                                     | mixed decimal                   |                         |

*1 (0.0은 구현에 의해 혼동되므로 설명이 없습니다)
*2 (0.0은 값이며, 1.0draft는 기본 값으로부터의 오프셋입니다(2019/12/6 미정의))
*3 transparentWithZWrite는 alphaMode=transparent 및 extensions.vrmc_materials_mtoon.transparentWithZWrite=true 에 대응
