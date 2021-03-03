# Short Notes - preparing HEVC data for decoding

(https://github.com/abdullahfarwees/H265-HEVC-ShortNotes/blob/main/resources/Heading-hevc.png)

This document gives the basic idea about the h265 bitstream data and compose it for decoding.

## NAL unit type of HEVC.
0-47 -> pass to decoder
48 - 63 -> Do NOT pass to decoder

## NAL units
0 - 9 -> coded slice segment
10 - 15 -> Reversed
16 - 21 -> coded slice segment
22 - 31 -> Reversed
32 - 40 
[
32-vps
33-sps
34-pps
35-AUD
36-EOS
37-EoB
38-FillerData
39-SEI (prefix)
40-SEI (postfix)
]

41 to 48 -> reversed
49 - 63 -> unspecified

### notable unit type of hevc decoding are listed below

IFrames (IRAP)
16 - BLA_N_LP 
17 - BLA_W_LP
18 - BLA_W_RADL
19 - CRA_NUT 
20 - IDR_N_LP
21 - IDR_W_RADL

PFrame
0 - TRAIL_N
1 - TRAIL_R
2 - TSA_N
3 - TSA_R
4 - STSA_N
5 - STSA_R
6 - RADL_R
7 - RADL_N
8 - RASL_N
9 - RASL_R

#### case 48:
Aggregate packets
expected header size

#### case 49: 
startbit, endbit

#### Basic Parser 
* stream must start with 00 00 00 001. skip any other input if first byte misses
* then save every byte untill receives the EoF. Also make a note of first byte, because it contains nal unit type.
* if EoF seen, we know any remaining unparsed data form a complete NAL unit.

[HEVCesBrowser](https://github.com/virinext/hevcesbrowser) - An open source tool to analyze the bitstream of h265 data!. 


#### Basic media network data
[](https://github.com/abdullahfarwees/H265-HEVC-ShortNotes/blob/main/resources/BasicMediaNetworkData.png)
- remove 12 bytes of RTP header
- remove 3 bytes of Fu Header
- combine all Fu packets till end packet is formed. after getting a complete data, its time to feed to the decoder.

#### Bitstream notes 

![HEVC BITSTREAM](https://github.com/abdullahfarwees/H265-HEVC-ShortNotes/blob/main/resources/bitstream-picture.png)

* Picture partioned into one or more slices
* First slice segment which is a independent slice. subsequent slides are depend on the previous slides.
*  2 classes NAL 
	** VCL
	** non-VCL

#### NAL unit header
* Both VCL and non-VCL has NAL unit header.
* First bit is 0 ( always ) 
* 6 bit contain type of NAL unit.
* there are 64 NAL units presents in HEVC. VCL (32) + non-VCL (32) -> 64
* layer identifier. but there are 6 bits remaining. Last 3 bits are temporal identifier.
* HEVC AU belongs to temporal sublayer.
* ALL VCL NAL belong to same AU and must have same TID in headers.

#### H265 decoding sequence
h265decoder-> hevcStart -> findAllNALs -> findStartofNal (loop untill success) -> pushframe -> stop -> DisposeGarbageData

useful Resources:
[RTP Payload format](https://tools.ietf.org/html/rfc7798)
[The Structure of BitStream](https://www.codeproject.com/Tips/896030/The-Structure-of-HEVC-Video)
