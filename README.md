#  RTP Payload for V-DMC

This repository is used for discussion of a personal draft for RTP Payload for V-DMC (draft-hsyang-avtcore-rtp-vdmc). 

* Current draft in [IETF Datatracker](https://datatracker.ietf.org/doc/draft-hsyang-avtcore-rtp-haptics/).
* [Current version draft in Github] (https://github.com/7439henry/draft-hsyang-avtcore-rtp-haptics/blob/main/Draft/draft-hsyang-avtcore-rtp-haptics-00.txt)

## Copy of abstract
This memo outlines RTP payload formats for the Video-based Dynamic Mesh Coding (V-DMC), which comprises several types of components, such as a basemesh, AC-based displacements, 2D representations of attributes, and an atlas.  This document focuses on describing the basemesh and displacement, while the RTP payload formats for the atlas and attributes are addressed in other documents.  The RTP payload header formats enable the packetization of a basemesh or displacement Network Abstraction Layer (NAL) unit in an RTP packet payload as well as fragmentation of a NAL unit into multiple RTP packets.

## Building the draft

The draft can be built into .txt or .xml files using [kramdown-rfc](https://github.com/cabo/kramdown-rfc). Please see details about kramdown-rfc usage from the related github project. 

Alternatively, [IETF conversion tool](https://author-tools.ietf.org/) can be used without installing additional software packages.

## Contributing

All material in this repository is considered as contributions to the IETF Standards Process and the applicaple policies shall apply. See guidelines for [contributions](https://github.com/7439henry/draft-hsyang-avtcore-rtp-haptics/blob/main/CONTRIBUTING.md)
