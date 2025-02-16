---
label: AVIF
sidebar_position: 6
---

# AVIF

:::info Under Maintenance
The content in this entry is incomplete & is in the process of being completed.
:::

AVIF, which stands for AV1 Image File Format, is a newer image codec that is based on the [AV1](../video/AV1.md) video codec. AVIF supersedes [HEIC](HEIC.md), & uses the same HEIF container as HEIC. AVIF is designed to have a better featureset & better general lossy compression than older image codecs, including [WebP](WebP.md), HEIC, & [JPEG](JPEG.md). AVIF is often compared to [JPEG-XL](JXL.md), though in practice, the two have very different strengths.

There are two AVIF profiles available for encoding: Baseline & Advanced, which are based on AV1's Main & High profiles respectively. The AVIF Baseline profile supports up to 8,192\*4,352 resolution specified by the requirement of using AV1 Level 5.1 or lower. Using tiling, it is possible to increase the maximum resolution of the AVIF Baseline profile to 65536\*65536, although this hurts coding efficiency as visual anomalies may be encountered along the edges of the tile boundaries. AVIF is also limited to 10 bit color precision in its Baseline profile.

In the AVIF Advanced profile, the maximum image dimensions extend to 16,384*8,704. Tiling may be used in the Advanced profile to create larger images, but the same limitations regarding visual artifacts apply. The AVIF Advanced profile extends the allowed AV1 Level to 6.0 or lower, & the highest bit depth offered by this profile is 12 BPC. It is worth noting that while it is currently a near certainty that AVIF implementations will support both the Baseline & Advanced profiles, this may not always be the case. This is a problem that affects HEIC currently, & is a known potential weakness of video-based image codecs.

## Performance Checklist

Lossless? *Poorly*

Lossy? *Yes*

Supported Bit Depths:
*8 BPC, 10 BPC, 12 BPC*

HDR/Wide Gamut? *Yes*

Animation? *Yes*

Transparency? *Yes*

Progressive Decode? *No*

Royalty Free? *Yes*

**Compatible Browsers** (full support)

- [Google Chrome](https://www.google.com/chrome/) 85+

- [Safari](https://www.apple.com/safari/) 16.4+

- [Firefox](https://www.mozilla.org/en-US/firefox/new/) 113+

- [Opera](https://www.opera.com/) 71+

- [GNOME Web](https://apps.gnome.org/app/org.gnome.Epiphany/)

- [Thorium](https://thorium.rocks/)

- [Mercury](https://thorium.rocks/mercury)

## Pros

AVIF is known for its extremely strong lossy compression performance for non-photographic images as well as photographic images in the low to medium fidelity range. AVIF is consistently better than JPEG visually, except for with complex images that contain a lot of highly entropic data like random noise.

AVIF compatibility has grown rapidly since its adoption in Google Chrome in 2020. For a relatively new image format, its level of penetration has been stellar, especially in the browser market. At this point in time, it would be a safe bet to ship AVIF images for your site given they compress better than JPEG & have older formats provided as fallbacks.

AVIF's wider featureset enables new experiences through images, including HDR. AVIF also presents astonishing animation prowess, as it is capable of using AV1's inter-frame coding techniques which make it easily the best animated image format for most use cases where it is compatible.

## Cons

AVIF encoding implementations are difficult to use, and images require much longer encoding times for what can be considered competitive quality. Making encoding more difficult, AVIF's use of intra-frame coding techniques that share data between blocks reduces parallelization capability & worsens generation loss. Theoretically, this improves coding efficiency, though. Via the AVIF Encoding section of the aomenc page:

<details>
  <summary>AVIF Encoding with aomenc through avifenc</summary>
  <div>
    <p>Using aomenc through avifenc is widely considered to be the best way to encode AVIF images, as SVT-AV1 only supports 4:2:0 chroma subsampling, rav1e isn't fast enough for still images, & the libaom team have put more effort into intra coding than the teams responsible for producing the other prominent open source AV1 encoders.</p>
    <p>A sample command for encoding AVIF looks like this:</p>
    <pre><code>avifenc -c aom -s 4 -j 8 -d 10 -y 444 --min 1 --max 63 -a end-usage=q -a cq-level=16 -a tune=ssim [input] output.avif</code></pre>
    <p>Where:</p>
    <ul>
      <li>`-c aom` is the encoder</li>
      <li>`-s 4` is the speed. Speeds 4 & below offer the best compression quality at the expense of longer encode times.</li>
      <li>`-j 8` is the number of threads the encoder is allowed to use. Increasing this past 12 will sometimes hurt encode times, as AVIF encoding via aomenc doesn't paralellize perfectly. Test using a speed benchmark to verify which value works best for you.</li>
      <li>`-d 10` is the bit depth. Specifying a value below 10 isn't recommended, as it will hurt coding efficiency even with an 8 bit source image.</li>
      <li>`-y 444` is the chroma subsampling mode. 4:4:4 chroma subsampling tends to provide better compression than 4:2:0 with AVIF, though on some images 4:2:0 chroma subsampling might be the better choice.</li>
      <li>`cq-level=16` is how you specify quality. Lower values correspond to higher quality & filesize, while higher values mean a smaller, lower-quality output is desired. This is preceded by `-a` because it is an aomenc option, not an avifenc one.</li>
      <li>`tune=ssim` is how the encoder handles RDO (rate-distortion optimization). This may be redundant with the default aomenc parameters, but specifying doesn't hurt to avoid an unintended change if a default is modified sometime in the future.</li>
    </ul>
  </div>
</details>

Additionally, AVIF tends to be underwhelming at high fidelity with photographic images. Compared to older codecs it usually outperforms the competition, but since medium to high fidelity tends to be the target for a lot of modern web delivery, it is disappointing to see AVIF not performing as well here.

AVIF also does not have progressive decode. This is a common weakness of video-based image codecs. While there is a hacky way to do progressive AVIF by encoding a low fidelity frame & then a high fidelity frame in an animated AVIF at a high framerate so the low fidelity frame is loaded & plays first, this is far from ideal for the average user & adds to an already burdensome encoding process. Additionally, this has issues with Firefox, which only recently got support for animated AVIF.

Finally, AVIF's lossless mode is incredibly underwhelming, often producing larger files than PNG. When compressing losslessly, avoid AVIF entirely.

## Conclusion

While AVIF is certainly promising, its shortcomings