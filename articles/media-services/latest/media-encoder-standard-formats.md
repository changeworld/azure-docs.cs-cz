---
title: Standardní formáty kodérů a kodeky – Azure
description: Tento článek obsahuje seznam nejběžnějších formátů souborů pro import a export, které lze použít s standardencoderpreset.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251359"
---
# <a name="standard-encoder-formats-and-codecs"></a>Standardní formáty kodéru a kodeky

Tento článek obsahuje seznam nejběžnějších formátů souborů pro import a export, které lze použít s [standardencoderpreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Informace o tom, jak vytvořit vlastní přednastavení pomocí **standardencoderpreset**, naleznete [v tématu Vytvoření transformace s vlastním přednastavením](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Vstupní formáty kontejnerů/souborů

| Formáty souborů (přípony souborů) | Podporuje se |
| --- | --- |
| FLV (s kodeky H.264 a AAC) (.flv) |Ano |
| MXF (.mxf) |Ano |
| GXF (.gxf) |Ano |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Ano |
| Windows Media Video (WMV) a ASF (.wmv, .asf) |Ano |
| AVI (nekomprimovaný s 8bitovým nebo 10bitovým kódováním) (.avi) |Ano |
| MP4 (.mp4, .m4a, .m4v) a ISMV (.isma, .ismv) |Ano |
| [Microsoft Digital Video Recording (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Ano |
| Matroska a WebM (.mkv) |Ano |
| WAVE a WAV (.wav) |Ano |
| QuickTime (.mov) |Ano |

### <a name="audio-formats-in-input-containers"></a>Zvukové formáty ve vstupních kontejnerech

Standardní kodér podporuje přenášení následujících zvukových formátů ve vstupních kontejnerech:

* Soubory MXF, GXF a QuickTime, které mají zvukové stopy s proloženými stereo nebo 5,1 vzorky

– nebo –

* Soubory MXF, GXF a QuickTime, kde se zvuk přenáší jako samostatné stopy PCM, ale mapování kanálů (na stereo nebo prostorový zvuk 5.1) je možné odvodit z metadat souboru

## <a name="input-video-codecs"></a>Vstupní video kodeky
| Vstupní video kodeky | Podporuje se |
| --- | --- |
| AVC s 8bitovou nebo 10bitovou hloubkou až do vzorkování 4:2:2, včetně AVCIntra |8bitová hloubka, 4:2:0 a 4:2:2 |
| Avid DNxHD (v souborech MXF) |Ano |
| DVCPro a DVCProHD (v souborech MXF) |Ano |
| Digitální video (DV) (v souborech AVI) |Ano |
| JPEG 2000 |Ano |
| MPEG-2 (až do profilu 422 a vysoké úrovně; včetně variant, jako jsou XDCAM, XDCAM HD, XDCAM IMX, CableLabs® a D10) |Až do profilu 422 |
| MPEG-1 |Ano |
| VC-1 a WMV9 |Ano |
| Canopus HQ a HQX |Ne |
| MPEG-4 Part 2 |Ano |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Ano |
| Nekomprimovaný formát YUV420 nebo Mezzanine |Ano |
| Apple ProRes 422 |Ano |
| Apple ProRes 422 LT |Ano |
| Apple ProRes 422 HQ |Ano |
| Apple ProRes Proxy |Ano |
| Apple ProRes 4444 |Ano |
| Apple ProRes 4444 XQ |Ano |
| HEVC/H.265| Hlavní profil|

## <a name="input-audio-codecs"></a>Vstupní zvukové kodeky
| Kodeky vstupního zvuku | Podporuje se |
| --- | --- |
| AAC (AAC-LC, AAC-HE a AAC-HEv2; až do prostorového zvuku 5.1) |Ano |
| MPEG Layer 2 |Ano |
| MP3 (MPEG-1 Audio Layer 3) |Ano |
| Windows Media Audio |Ano |
| WAV a PCM |Ano |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Ano |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Ano |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Ano |
| AMR (adaptivní s více rychlostmi) |Ano |
| AES (SMPTE 331M a 302M, AES3-2003) |Ne |
| Dolby® E |Ne |
| Dolby® Digital (AC3) |Ne |
| Dolby® Digital Plus (E-AC3) |Ne |

## <a name="output-formats-and-codecs"></a>Výstupní formáty a kodeky
V následující tabulce jsou uvedeny kodeky a formáty souborů, které jsou podporovány pro export.

| Formát souboru | Kodek videa | Zvukový kodek |
| --- | --- | --- |
| MP4 <br/><br/>(včetně vícebitových kontejnerů MP4) |H.264 (profily vysokých, hlavních a směrných plánů) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (profily vysokých, hlavních a směrných plánů) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Další kroky

[Vytvoření transformace s vlastním přednastavením](customize-encoder-presets-how-to.md)
