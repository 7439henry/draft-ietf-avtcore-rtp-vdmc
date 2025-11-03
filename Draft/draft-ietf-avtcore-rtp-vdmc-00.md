---
title: "RTP Payload Format for V-DMC"
abbrev: RTP-Payload-VDMC
docname: draft-ietf-avtcore-rtp-vdmc-00
date: {DATE}
stream: IETF
category: std
ipr: trust200902
area: Transport
workgroup: avtcore

stand_alone: yes
pi: [toc, sortrefs, symrefs, docmapping]

author:
  -
    ins: HS. Yang
    name: Hyunsik Yang
    org: InterDigital
    email: hyunsik.yang@interdigital.com
    country: USA
  -
    ins: X. de Foy
    name: Xavier de Foy
    org: InterDigital
    email: xavier.defoy@interdigital.com
    country: Canada

normative:


  ISO.IEC.23090-29:
    title: "Information technology - Coded representation of immersive media - Part 29: Video-based dynamic mesh coding (V-DMC)"
    author:
      org: "ISO/IEC"
    date: 2024
    seriesinfo:
      ISO/IEC: 23090-29
    target: https://www.iso.org/standard/85254.html 

  ISO.IEC.23090-05:
    title: "Information technology - Coded representation of immersive media - Part 5: Visual volumetric video-based coding (V3C) and video-based point cloud compression (V-PCC)"
    author:
      org: "ISO/IEC"
    date: 2023
    seriesinfo:
      ISO/IEC: 23090-05
    target: https://www.iso.org/standard/83535.html

  ISO.IEC.23090-12:
    title: "Information technology - Coded representation of immersive media - Part 12: MPEG Immersive video (MIV)"
    author:
      org: "ISO/IEC"
    date: 2023
    seriesinfo:
      ISO/IEC: 23090-12
    target: https://www.iso.org/standard/79113.html
    
  ISO.IEC.23090-10:
    title: "Information technology - Coded representation of immersive media - Part 10: Carriage of visual volumetric video-based coding data"
    author:
      org: "ISO/IEC"
    date: 2022
    seriesinfo:
      ISO/IEC: 23090-10
    target: https://www.iso.org/standard/78991.html

informative:
  RFC2119:
  RFC2736:
  RFC8088:
  RFC8174:
  RFC3550:
  RFC7798:
  RFC8866:
  RFC9143:
  RFC3551:
  RFC4585:
  RFC3711:
  RFC5124:
  RFC7201:
  RFC7202:
  RFC9328:
  
  
  I-D.ietf-avtcore-rtp-v3c:
  
  
--- abstract

This memo outlines RTP payload formats for the Video-based Dynamic Mesh Coding (V-DMC), which comprises several types of components, such as a basemesh, AC-based displacements, 2D representations of attributes, and an atlas. This document focuses on describing the basemesh and displacement, while the RTP payload formats for the atlas and attributes are addressed in other documents. The RTP payload header formats enable the packetization of a basemesh or displacement Network Abstraction Layer (NAL) unit in an RTP packet payload as well as fragmentation of a NAL unit into multiple RTP packets.

--- middle

# Introduction
The advancements in 3D capture, modeling, and rendering have greatly expanded the presence of 3D content across various platforms and devices. However, when left uncompressed, 3D content can incur considerable costs in terms of storage and transmission.

In response to this challenge, a visual volumetric video-based coding (V3C) standard {{ISO.IEC.23090-05}} has been developed to cater to the growing demand for efficient handling of 3D content. V3C is a generic mechanism for volumetric video coding, and it can be used by applications targeting volumetric content. A high-level description of V3C is provided by {{I-D.ietf-avtcore-rtp-v3c}}. V3C applications today target volumetric data types such as point clouds with Video-based Point Cloud Compression (V-PCC){{ISO.IEC.23090-05}} and immersive video with depth, with MPEG Immersive Video (MIV) {{ISO.IEC.23090-12}}.

3D mesh is another widely utilized technology for representing immersive content. 3D meshes are continuously evolving to become more sophisticated, inevitably leading to an increase in mesh size. Additionally, dynamic mesh sequences demand substantial data volumes due to the significant amount of temporal information they contain. With respect to this evolution, the V-DMC standard has been developed to facilitate the compression of 3D mesh data using the V3C standard {{ISO.IEC.23090-05}}.

V-DMC is a V3C application which consists of several V3C sub-components: Atlas, Attributes, Basemesh, and AC-based Displacement. The RTP payloads for the Atlas and Attributes sub-components are described in {{I-D.ietf-avtcore-rtp-v3c}}, which defines an RTP payload format for the Atlas sub-component, and refers to existing 2D video RTP payload formats for attributes. In contrast, the Basemesh sub-component in V-DMC utilizes a new codec defined in appendix H of {{ISO.IEC.23090-29}}, requiring the definition of a new RTP payload. The AC-based displacement sub-component may be coded using Arithmetic Coding (AC) as specified in appendix J of {{ISO.IEC.23090-29}}, thus requiring the definition of a new RTP payload for AC-based displacement. The displacement sub-component may alternatively be coded using a 2D video codec, and in this case may be transported using 2D video RTP payload formats, (e.g., {{RFC9328}}, {{RFC7798}}). Therefore, this memo describes RTP payload headers for the basemesh codec and the AC-based displacement codec defined by MPEG in {{ISO.IEC.23090-29}} (appendix H and J, respectively). 

Furthermore, the basemesh and AC-based displacement RTP payload formats defined in this memo, and their codecs defined in {{ISO.IEC.23090-29}} appendix H and J respectively, can also be used in a non-V-DMC context. This document followed recommendations in {{RFC8088}} and {{RFC2736}} for RTP payload format writers.


# Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} when, and only when, they appear in all capitals, as shown here.


# Definition, and abbreviations

## General

This document uses the definitions of the Video-based dynamic mesh coding {{ISO.IEC.23090-29}}. Some of these terms are provided here for convenience.

## Definitions from the V-DMC specification
The following definitions are added to the ones found in section 3.1.2 of {{I-D.ietf-avtcore-rtp-v3c}}.

attribute map : image for mapping an attribute into a surface of 3D shape.

basemesh: a simplified low-resolution approximation of the original mesh. 

displacement : a set of 3D vectors that are added to the vertices of the subdivided mesh to approximate closely the input mesh surface.

submesh : independently decodable region of a basemesh

subdivision : process of dividing the mesh faces into a number of sub-faces

## Abbreviation
The following abbreviations are added to the ones found in section 3.2 of {{I-D.ietf-avtcore-rtp-v3c}}.

BMD Basemesh Data

CDS Coded Displacement Sequence

CBMS Coded BaseMesh Sequence

LOD Level Of Detail 

V-DMC Video-based Dynamic Mesh Coding

# V-DMC format Description {#vdmc-format-dsecription}

## Overview of V-DMC (informative)

V-DMC is a V3C application, which uses the framework described in section 4 of {{I-D.ietf-avtcore-rtp-v3c}}. As such, a V-DMC encoder converts a 3D representation into multiple representations, the V3C components, and generates associated data, the atlas, which documents this conversion and enables the reconstruction of the 3D representation by a decoder.

The other V3C components used in V-DMC are a basemesh, AC-based displacement, and attributes providing properties such as mesh and connectivity information, vertex displacement information and texture or material information respectively. {{figure-v-dmc-structure}} represents the 4 types of V3C components altogether used in V-DMC, including these V3C video components and the V3C atlas component.

The basemesh component represents a simplified version of the detailed mesh describing the object. The simplified mesh can be encoded using any mesh codec. {{ ISO.IEC.23090-29}} defines a static mesh codec that can used by the generic mechanism of the basemesh codec defined in {{ ISO.IEC.23090-29}}. The AC-based displacement component provides displacement information which should be applied to the subdivided basemesh to obtain adetailed mesh. The displacement component can be encoded either using an arithmetic displacement codec described in {{ ISO.IEC.23090-29}}, or alternatively it can be encoded as a V3C geometry video component i.e., V3C_GVD using any 2D video codec, indicated by the profile or using an SEI message. The attribute components can provide additional properties such as texture or material information and can be encoded by any video codec. Atlas component provides information to a V3C decoding and rendering system on how to perform inverse reconstruction. The atlas component codec is described in {{ ISO.IEC.23090-05}} and an extension of the V3C atlas codec for V-DMC is described in {{ ISO.IEC.23090-29}}.


~~~~~~~~~~
+------------+ Atlas        +------------+
|   Atlas    | sub-bitstream|   Atlas    |
|  encoder   | ------------>|  decoder   |
+------------+              +------------+
+------------+ BaseMesh     +------------+
|  BaseMesh  | sub-bitstream|  BaseMesh  |
|  encoder   | ------------>|  decoder   |
+------------+              +------------+
+------------+ Displacement +------------+
|Displacement| sub-bitstream|Displacement|
|  encoder   | ------------>|  decoder   |
+------------+              +------------+
+------------+ Attribute    +------------+
|   Video    | sub-bitstream|   Video    |
|  encoder   | ------------>|  decoder   |
+------------+              +------------+
~~~~~~~~~~
{: #figure-v-dmc-structure title="V-DMC streaming structure"}



Similarly to other V3C applications, in V-DMC, the binary form of the V3C components, i.e., video bitstreams, basemesh bitstream and V3C atlas component, i.e., atlas bitstream, can be grouped and represented by a single V-DMC bitstream. The V-DMC bitstream is composed of a set of V3C units. Each V3C unit has a V3C unit header and a V3C unit payload. The V3C unit header describes the V3C unit type for the payload. V3C unit payload contains V3C video components, V3C non-video components such as basemesh and AC-based displacement, V3C atlas component or a V3C parameter set. V3C components, i.e., basemesh, displacement, or attribute components, correspond to NAL-based data units (e.g., NAL units for basemesh and AC-based displacement are defined in {{ ISO.IEC.23090-29}}). The NAL units for attribute and video-based displacement are defined in therespective specification that could be decoded by appropriate decoders. An example of V-DMC bitstream consisting of a V3C parameter set, atlas bitstream, one video component bitstream (attribute) and two non-video component bitstreams(basemesh, displacement) is provided in {{figure-v-dmc-bitstream}}.

~~~~~~~~~~
  +-------------------+------------------+-------------------+
  | V3C Unit(V3C_VPS) | V3C Unit(V3C_AD) | V3C Unit(V3C_BMD) |
  +-------------------+------------------+-------------------+
  | V3C Unit(V3C_ADD) | V3C Unit(V3C_AVD)| V3C Unit(V3C_CAD) | ...
  +-------------------+------------------+-------------------+ 
~~~~~~~~~~
{: #figure-v-dmc-bitstream title="Example of V-DMC bitstream"}


## V3C Parameter set for V-DMC {#v3c-parameter}

The V3C parameter set(VPS) is encapsulated in its own V3C unit, which allows decoupling the transmission of the V3C parameter set from the V3C video, non-video and atlas components. The VPS may be transmitted by external means (e.g., as a result of the capability exchange) or through a (reliable or unreliable) control protocol. Section 9 of {{I-D.ietf-avtcore-rtp-v3c}} provides information on how a V3C parameter set may be signaled as part of session description protocol.

V-DMC, since it is a V3C application, uses the V3C parameter set and extends it with additional parameters, defined in {{ISO.IEC.23090-29}}. Section 9 of this memo provides information on these additional parameters and their signaling in SDP. V3C parameter set signaling in V-DMC may include V3C parameters described in section 9 of {{I-D.ietf-avtcore-rtp-v3c}} and in the section 9 of this memo.

## Atlas, Video and non-Video Components for V-DMC (informative) {#section4}

In a V-DMC bitstream the atlas component is identified by a vuh_unit_type field equal to V3C_AD, in the V3C unit header. The atlas component consists of atlas NAL units that define header and payload pairs, see {{atlas-nal}}. V-DMC video components are identified by a vuh_unit_type field equal to V3C_GVD or V3C_AVD. The base mesh information is present in a non-video component identified by V3C_BMD, which consists of basemesh NAL units that define header and payload pairs, see {{basemesh-nal}}. The AC-based displacement information is present in a non-video component identified by V3C_ADD. The component identified by V3C_ADD consists of displacement NAL units that define header and payload pairs, see {{displ-nal}}. The V3C video attribute components with vuh_unit_type V3C_AVD can be further differentiated by other fields in the V3C unit header such as vuh_attribute_index, vuh_attribute_partition_index, vuh_map_index and vuh_auxiliary_video_flag, which are described further below. By mapping V3C parameter set information to vuh_attribute_index, a V3C decoder identifies which attribute a given V3C video component contains, e.g., color.

The information supplied by a V3C unit header should be provided in one form or another to a V3C decoder, e.g., as part of SDP as described in this memo in {{session-descp}} or in-band. The four-byte V3C unit header syntax and semantics are copied below as defined in {{ISO.IEC.23090-29}}, but the syntax is subject to change. Implementations should always refer to the latest specification of {{ISO.IEC.23090-29}}. The syntax of four-byte V3C unit header is provided here for informative purposes only.



~~~~~~~~~~

   v3c_unit_header( ) {
    unsigned int(5) vuh_unit_type;
    if( vuh_unit_type == V3C_AVD || vuh_unit_type == V3C_GVD ||
       vuh_unit_type == V3C_OVD || vuh_unit_type == V3C_AD || 
       vuh_unit_type == V3C_CAD || vuh_unit_type == V3C_PVD || 
       vuh_unit_type == V3C_BMD || vuh_unit_type == V3C_ADD){ 
       unsigned int(4) vuh_v3c_parameter_set_id;
     }
     if( vuh_unit_type == V3C_AVD || vuh_unit_type == V3C_GVD ||
       vuh_unit_type == V3C_OVD || vuh_unit_type == V3C_AD ||
       vuh_unit_type == V3C_PVD || vuh_unit_type == V3C_BMD) ||
       vuh_unit_type == V3C_ADD ) {
       unsigned int(6) vuh_atlas_id;
     }
     if( vuh_unit_type == V3C_AVD ) {
       unsigned int(7) vuh_attribute_index;
       unsigned int(5) vuh_attribute_partition_index;
       unsigned int(4) vuh_map_index;
       unsigned int(1) vuh_auxiliary_video_flag;
     }
     else if( vuh_unit_type == V3C_GVD ) {
       unsigned int(4) vuh_map_index;
       unsigned int(1) vuh_auxiliary_video_flag;
       bit(12) vuh_reserved_zero_12bits;
     }
     else if( vuh_unit_type == V3C_OVD || vuh_unit_type == V3C_AD ||
         vuh_unit_type == V3C_PVD || vuh_unit_type == V3C_BMD ||
         vuh_unit_type == V3C_ADD) {
       bit(17) vuh_reserved_zero_17bits;
     }
     else if( vuh_unit_type == V3C_CAD ) {
       bit(23) vuh_reserved_zero_23bits;
     }
     else {
       bit(27) vuh_reserved_zero_27bits;
     }
   }

~~~~~~~~~~



vuh_unit_type indicates the V3C unit type for the V3C component as specified in {{ISO.IEC.23090-29}}. As a convenience, the mapping table from vuh_unit_type values to semantics is copied below in {{figure-v3ctype}}.

~~~~~~~~~~

+--------+----------+--------------------+------------------------+
|vuh unit|Identifier|    V3C unit type   |     Description        |
|  type  |          |                    |                        |
+--------+----------+--------------------+------------------------+
|   0    | V3C_VPS  |  V3C parameter set |   V3C level parameters |
+--------+----------+--------------------+------------------------+
|   1    | V3C_AD   |     Atlas data     |   Atlas information    |
+--------+----------+--------------------+------------------------+
|   2    | V3C_OVD  |Occupancy video data|   (Not used in V-DMC)  |
+--------+----------+--------------------+------------------------+
|   3    | V3C_GVD  |Geometry video data |Displacement information|
|        |          |                    |(Displacement when      |
|        |          |                    |using a video-based     |
|        |          |                    |codec)                  |
+--------+----------+--------------------+------------------------+
|   4    | V3C_AVD  |Attribute video data|  Attribute information |
+--------+----------+--------------------+------------------------+
|   5    | V3C_PVD  | Packed video data  |  (Not used in V-DMC)   |
+--------+----------+--------------------+------------------------+
|   6    |          | Common atlas data  |Information that is     |
|        |          |                    |common for atlases      |
|        | V3C_CAD  |                    |in a CVS(Specified in   |
|        |          |                    |ISO/IEC 23090-12)       |
+--------+----------+--------------------+------------------------+
|   7    | V3C_BMD  |   Basemesh data    |  Basemesh information  |
+--------+----------+--------------------+------------------------+
|        | V3C_ADD  |  Arithmetic coded  |Displacement information|
|   8    |          | displacement data  |                        |
+--------+----------+--------------------+------------------------+

~~~~~~~~~~
{: #figure-v3ctype title="V3C component for V-DMC"}

   vuh_v3c_parameter_set_id specifies the value of
   vps_v3c_parameter_set_id for the active V3C VPS.

   vuh_atlas_id specifies the ID of the atlas that corresponds to the
   current V3C unit.

   vuh_attribute_index indicates the index of the attribute data carried
   in the Attribute Video Data unit.

   vuh_attribute_partition_index indicates the index of the attribute
   dimension group carried in the attribute video data unit.

   vuh_map_index when present indicates the map index of the current
   geometry or attribute stream.  When not present, the map index of the
   current geometry or attribute sub-bitstream is derived based on the
   type of the sub-bitstream.
   
   vuh_auxiliary_video_flag equal indicates if the associated geometry
   or attribute video data unit is a RAW and/or EOM coded points video
   only sub-bitstream.  

### Atlas NAL units {#atlas-nal}

The atlas component provides information to a V3C decoding and/or rendering system on how to perform inverse reconstruction. V-DMC uses an atlas component based on the V3C atlas, including new V-DMC specific parameters defined in {{ISO.IEC.23090-29}}, which do not impact the payload format. For example, the V-DMC atlas component indicates how to perform the subdivision of the basemesh, how to apply the AC-based displacement information to the subdivided mesh vertices and how to apply attributes to the reconstructed mesh.

The V3C atlas RTP payload format described in {{ISO.IEC.23090-05}} can be used to transport the V-DMC atlas component. Section 4.3.2 of {{I-D.ietf-avtcore-rtp-v3c}} includes a copy of the syntax and semantics of the V3C atlas NAL unit.



### Basemesh NAL units

The basemesh component is a simplified low-resolution approximation of the original mesh. The basemesh component can be encoded using any mesh codec, including the basemesh codec described in {{ISO.IEC.23090-29}}. When employing the basemesh codec described in {{ISO.IEC.23090-29}}, data can be transmitted using the RTP payload format specified in {{section5}}. 

The basemesh NAL unit (nal_unit(BmNumBytesInNalUnit)) is a byte-aligned syntax structure defined by {{ISO.IEC.23090-29}} to carry base mesh data. A basemesh NAL unit always contains a 16-bit NAL unit header (bmesh_nal_unit_header()), which indicates among other things the type of the NAL unit (bmesh_nal_unit_type). The payload of a NAL unit refers to the NAL unit excluding the NAL unit header. The basemesh NAL unit syntax and semantics are copied here as defined in {{ISO.IEC.23090-29}}.


~~~~~~~~
bmesh_nal_unit_header( ) {
    bit(1) bmesh_nal_forbidden_zero_bit
    bit(6) bmesh_nal_unit_type
    bit(6) bmesh_nal_layer_id
    bit(3) bmesh_nal_temporal_id_plus1
}
nal_unit(BmNumBytesInNalUnit){
    bmesh_nal_unit_header();
    BmNumBytesInRbsp = 0;
    for( i = 2; i < BmNumBytesInNalUnit; i++ )
      bit(8) rbsp_byte[ BmNumBytesInRbsp++ ];
}
~~~~~~~~~~


bmesh_nal_forbidden_zero_bit MUST be equal to 0. 

bmesh_nal_unit_type specifies the type of the RBSP data structure contained in the NAL unit as specified in {{ ISO.IEC.23090-29}}. In particular, the basemesh NAL unit types supported are specified in Table H.1 of  {{ ISO.IEC.23090-29}}.

bmesh_nal_layer_id specifies the identifier of the layer to which an BMCL NAL unit belongs or the identifier of a layer to which a non-BMCL NAL unit applies. The value of bmesh_nal_layer_id shall be in the range of 0 to 62, inclusive. 

bmesh_nal_temporal_id_plus1 minus 1 specifies a temporal identifier for the NAL unit. The value of nal_temporal_id_plus1 shall not be equal to 0.

### AC-based Displacement NAL units

The displacement component provides displacement information, that are used to apply deformation on a mesh over time. The displacement component can be encoded using the arithmetic codec defined in Annex J of {{ISO.IEC.23090-29}}, or alternatively can be encoded as V3C geometry video component using traditional 2D video codec, when employing a 2D codec, data can be transmitted using the RTP payload format specified for the codec. When employing the arithmetic codec defined in Annex J of {{ISO.IEC.23090-29}}, data can be transmitted using the RTP payload format specified in {{section6}}.

The displacement NAL unit (nal_unit(NumBytesInNalUnit)) is a byte-aligned syntax structure defined by {{ISO.IEC.23090-29}} to carry displacement data. A displacement NAL unit always contains a 16-bit NAL unit header (displ_nal_unit_header()), which indicates among other things the type of the NAL unit (displ_nal_unit_type). The payload of a NAL unit refers to the NAL unit excluding the NAL unit header.  The displacement NAL unit syntax and semantics are copied here as defined in {{ISO.IEC.23090-29}}.

~~~~~~~~
displ_nal_unit_header( ) {
    bit(1) displ_nal_forbidden_zero_bit
    bit(6) displ_nal_unit_type
    bit(6) displ_nal_layer_id
    bit(3) displ_nal_temporal_id_plus1
}
nal_unit(NumBytesInNalUnit){
    displ_nal_unit_header();
    NumBytesInRbsp = 0;
    for( i = 2; i < NumBytesInNalUnit; i++ )
      bit(8) rbsp_byte[ NumBytesInRbsp++ ];
}
~~~~~~~~~~

displ_nal_forbidden_zero_bit MUST be equal to 0. 

displ_nal_unit_type specifies the type of the RBSP data structure contained in the NAL unit as specified in {{ISO.IEC.23090-29}}. In particular, the AC-based displacement NAL unit types supported are specified in Table J.1 of  {{ISO.IEC.23090-29}}.

displ_nal_layer_id is specifies the identifier of the layer to which an DCL NAL unit belongs or the identifier of a layer to which a non-DCL NAL unit applies. The value of displ_nal_layer_id shall be in the range of 0 to 62, inclusive.

displ_nal_temporal_id_plus1 is minus 1 specifies a temporal identifier for the NAL unit. The value of
nal_temporal_id_plus1 shall not be equal to 0.



# Payload format for V-DMC Basemesh {#section5}

## General

This section describes details related to the RTP payload format definitions for the V-DMC basemesh codec defined in Annex H of {{ISO.IEC.23090-29}}. Aspects related to RTP header, RTP payload header and general payload structure are considered. 

## RTP header Usage

The RTP header is defined in {{RFC3550}} and represented in {{figure-rtpheader}}. Some of the header field values are interpreted as follows.


~~~~~~~~~~

   0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |V=2|P|X|  CC   |M|     PT      |       sequence number         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                           timestamp                           |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           synchronization source (SSRC) identifier            |
   +=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
   |            contributing source (CSRC) identifiers             |
   |                             ....                              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   
~~~~~~~~~~
{: #figure-rtpheader title="RTP header for V-DMC Basemesh"}

   Marker bit (M): 1 bit

   Set for the last packet of the access unit, carried in the current
   RTP stream.  This is in line with the normal use of the M bit in
   video formats to allow an efficient playout buffer handling.

   Payload type (PT): 7 bits
   
   The assignment of a payload type MUST be performed either through the profile used or in a dynamic way.

   Sequence Number (SN): 16 bits

   Set and used in accordance with {{RFC3550}}
   
   Timestamp : 32 bits

   The RTP timestamp is set to the sampling timestamp of the content.  A
   90 kHz clock rate MUST be used.

   If the NAL unit has no timing properties of its own (e.g., 
   set and SEI NAL units), the RTP timestamp MUST be set to the RTP 
   timestamp of the coded basemesh of the access unit in which the NAL
   unit (according to Section H of {{ISO.IEC.23090-29}} is included.

   Receivers MUST use the RTP timestamp for the display process, even
   when the bitstream contains basemesh frame timing SEI messages as
   specified in {{ISO.IEC.23090-29}}.
   
   Synchronization source (SSRC): 32 bits 

   Used to identify the source of the RTP packets. By definition a
   single SSRC is used for all parts of a single bitstream. 
   The remaining RTP header fields are used as specified in {{RFC3550}}.

## RTP payload header for Basemesh {#basemesh-nal}

The RTP Payload Header follows the RTP header. {{figure-rtpheader-base}} describes RTP Payload Header.

~~~~~~~~~~
      0                   1
      0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
     +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
     |F|    NUT    |    NLI    | TID |
     +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-rtpheader-base title="RTP Payload header for Basemesh"}

F : bmesh_nal_forbidden_zero_bit specified in {{ISO.IEC.23090-29}} MUST be equal to 0. 

NUT : bmesh_nal_unit_type as specified in {{ISO.IEC.23090-29}} define the type of the RBSP data structure contained in the NAL unit as specified in {{ISO.IEC.23090-29}}. In particular, the basemesh NAL unit types supported are specified in Table H.1 of {{ ISO.IEC.23090-29}}.

NLI : bmesh_nal_layer_id as specified in {{ISO.IEC.23090-29}} defines the identifier of the layer to which an BMCL NAL unit belongs or the identifier of a layer to which a non-BMCL NAL unit applies. The value of nal_layer_id shall be in the range of 0 to 62, inclusive. 

TID : bmesh_nal_temporal_id_plus1 minus 1 as specified in {{ISO.IEC.23090-29}} defines a temporal identifier for the NAL unit. The value of bmesh_nal_temporal_id_plus1 shall not be equal to 0.


## Payload structures

### General

Three different types of RTP packet payload structures are specified. A receiver can identify the payload structure by the first two bytes of the RTP packet payload, which co-serves as the RTP payload header. These two bytes are always structured as a NAL unit header. The NAL unit type field indicates which structure is present in the payload.

The three different payload structures are as follows:

Single NAL Unit Packet: Contains a single NAL unit in the payload. This payload structure is specified in {{bmesh-single}}.

Aggregation Packet: Contains multiple NAL units in a single RTP payload. This payload structure is specified in {{bmesh-aggr}}.

Fragmentation Unit: Contains a subset of a single NAL unit. This payload structure is specified in {{bmesh-frag}}.

NOTE: (informative) This memo does not limit the size of NAL units encapsulated in NAL unit packets and fragmentation units. {{ISO.IEC.23090-29}} does not restrict the maximum size of a NAL unit directly either. Instead, a NAL unit sample stream format may be used, which provides flexibility to signal NAL unit size up to UINT64_MAX bytes.

### Single NAL unit packet {#bmesh-single}

Single NAL unit packet contains exactly one NAL unit and consists of an RTP payload header and following conditional fields: 16-bit DONL and 16-bit bmesh-sm-id. The rest of the payload data contain the NAL unit payload data (excluding the NAL unit header). Single NAL unit packet MUST only contain atlas NAL units of the types defined in Table H.1 of {{ISO.IEC.23090-29}}. The structure of the single NAL unit packet is shown below in {{figure-bsnal-unit}}.


~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |      RTP payload header       |      DONL (conditional)       |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |       bmesh-sm-id (cond)      |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
  |                                                               |
  |                        NAL unit data                          |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               :...OPTIONAL RTP padding        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-bsnal-unit title="Single NAL unit packet for Basemesh"}

RTP payload header MUST be an exact copy of the NAL unit header of the contained NAL unit.

A NAL unit stream composed by de-packetizing single NAL unit packets in RTP sequence number order MUST conform to the NAL unit decoding order, when DONL is not present.

The DONL field, when present, specifies the value of the 16-bit decoding order number of the contained NAL unit. If sprop-max-don-diff is greater than 0 for any of the RTP streams, the DONL field MUST be present, and the variable DONL for the contained NAL unit is derived as equal to the value of the DONL field. Otherwise (sprop-max-don-diff is equal to 0 for all the RTP streams), the DONL field MUST NOT be present.

The bmesh-sm-id field, when present, specifies the 16-bit submesh identifier for the NAL unit, as signaled in basemesh header defined in {{ISO.IEC.23090-29}}. If sprop-bmesh-sm-id-pres is equal to 1 and RTP payload header NUT is in range 0-29, inclusive, the bmesh-sm-id field MUST be present. Otherwise, the bmesh-sm-id field MUST NOT be present.

NOTE: (informative) Only values for NAL unit type (NUT) in range 0-29, inclusive, are allocated for bash mesh submesh layer unit data in {{ISO.IEC.23090-29}}.

### Aggregation packet {#bmesh-aggr}

Aggregation Packets (APs) enable the reduction of packetization overhead for small NAL units, such as most of the non-BMCL NAL units, which are often only a few octets in size.

Aggregation packets MAY be used wrap multiple NAL units belonging to the same access unit in a single RTP payload. The first two bytes of the AP MUST contain RTP payload header. The NAL unit type (NUT) for the NAL unit header contained in the RTP payload header MUST be equal to 45, which falls in the unspecified range of the NAL unit types defined in {{ISO.IEC.23090-29}}. AP MAY contain a conditional bmesh-sm-id field. AP MUST contain two or more aggregation units. The structure of AP is shown in {{figure-bsnal-ap}}.

~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |  RTP payload header (NUT=45)  |       bmesh-sm-id (cond)      |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                                                               |
  |                  Two or more aggregation units                |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               :...OPTIONAL RTP padding        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-bsnal-ap title="Aggregation Packet (AP) for Basemesh"}

The fields in the payload header are set as follows. The F bit MUST be equal to 0 if the F bit of each aggregated NAL unit is equal to zero; otherwise, it MUST be equal to 1. The NUT field MUST be equal to 45. The value of NLI MUST be equal to the lowest value of NLI of all the aggregated NAL units. The value of TID MUST be the lowest value of TID of all the aggregated NAL units.

All BMCL NAL units in an aggregation packet have the same TID value since they belong to the same access unit. However, the packet MAY contain non-BMCL NAL units for which the TID value in the NAL unit header MAY be different than the TID value of the BMCL NAL units in the same AP.

The bmesh-sm-id field, when present, specifies the 16-bit basemesh identifier for all BMCL NAL units in the AP. If sprop-bmesh-sm-id-pres is equal to 1, the bmesh-sm-id field MUST be present. Otherwise, the bmesh-sm-id field MUST NOT be present.

AP MUST carry at least two aggregation units (AU) and can carry as many aggregation units as necessary. However, the total amount of data in an AP MUST fit into an IP packet, and the size SHOULD be chosen so that the resulting IP packet is smaller than the MTU size so to avoid IP layer fragmentation. The structure of the AU depends both on the presence of the decoding order number, the sequence order of the AU in the AP and the presence of bmesh-sm-id field. The structure of an AU is shown in {{figure-ap-unit}}.

~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |  DOND (cond)  /  DONL (cond)  |       bmesh-sm-id (cond)      |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |            NALU size          |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
  |                                                               |
  |                            NAL unit                           |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-ap-unit title="Aggregation Unit (AU) for Basemesh"}

If sprop-max-don-diff is greater than 0 for any of the RTP streams, an AU begins with the DOND / DONL field. The first AU in the AP contains DONL field, which specifies the 16-bit value of the decoding order number of the aggregated NAL unit. The variable DON for the aggregated NAL unit is derived as equal to the value of the DONL field. All subsequent AUs in the AP MUST contain an (8-bit) DOND field, which specifies the difference between the decoding order number values of the current aggregated NAL unit and the preceding aggregated NAL unit in the same AP. The variable DON for the aggregated NAL unit is derived as equal to the DON of the preceding aggregated NAL unit in the same AP plus the value of the DOND field plus 1 modulo 65536.

When sprop-max-don-diff is equal to 0 for all the RTP streams, DOND / DONL fields MUST NOT be present in an aggregation unit. The aggregation units MUST be stored in the aggregation packet so that the decoding order of the containing NAL units is preserved. This means that the first aggregation unit in the aggregation packet SHOULD contain the NAL unit that SHOULD be decoded first.

If sprop-bmesh-sm-id-pres is equal to 2 and the AU NAL unit header type is in range 0-29, inclusive, the 16-bit bmesh-sm-id field MUST be present in the aggregation unit after the conditional DOND/DONL field. Otherwise bmesh-sm-id field MUST NOT be present in the aggregation unit.

The conditional fields of the aggregation unit are followed by a 16-bit NALU size field, which provides the size of the NAL unit (in bytes) in the aggregation unit. The remainder of the data in the aggregation unit SHOULD contain the NAL unit (including the unmodified NAL unit header).

### Fragmentation unit {#bmesh-frag}

Fragmentation Units (FUs) are introduced to enable fragmenting a single NAL unit into multiple RTP packets, possibly without co-operation or knowledge of the encoder. A fragment of a NAL unit consists of an integer number of consecutive octets of that NAL unit. Fragments of the same NAL unit MUST be sent in consecutive order with ascending RTP sequence numbers (with no other RTP packets within the same RTP stream being sent between the first and last fragment.

When a NAL unit is fragmented and conveyed within FUs, it is referred to as a fragmented NAL unit. Aggregation packets MUST NOT be fragmented. FUs MUST NOT be nested; i.e., an FU MUST NOT contain a subset of another FU. The RTP header timestamp of an RTP packet carrying an FU is set to the NALU-time of the fragmented NAL unit.

A FU consists of an RTP payload header with NUT equal to a 46, an 8-bit FU header, a conditional 16-bit DONL field, a conditional 16-bit bmesh-sm-id field and an FU payload. The structure of an FU is illustrated below in {{figure-aggr-unit}}.

~~~~~~~~~~
  0                   1                   2                   3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  RTP payload header (NUT=46)  |   FU header   |  DONL (cond)  |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  DONL (cond)  |    bmesh-sm-id  (cond)        |               |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+               |
 |                                                               |
 |                          FU payload                           |
 |                                                               |
 |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                               :...OPTIONAL RTP padding        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-aggr-unit title="Fragmentation Unit for Basemesh"}

## Packetization and De-packetization Rules


The following packetization rules apply for V-DMC data:

 - 	If sprop-max-don-diff is greater than 0 for any of the RTP streams, the transmission order of NAL units carried in the RTP stream MAY be different than the NAL unit decoding order and the NAL unit output order. Otherwise (sprop-max-don-diff is equal to 0 for all the RTP streams), the transmission order of NAL units carried in the RTP stream MUST be the same as the NAL unit decoding order.
 
 - 	A NAL unit of a small size SHOULD be encapsulated in an aggregation packet together with one or more other NAL units in order to avoid the unnecessary packetization overhead for small NAL units. For example, non-BMCL NAL units such as access unit delimiters, parameter sets, or SEI NAL units are typically small and can often be aggregated with BMCL NAL units without violating MTU size constraints.
 
 - 	Each non-BMCL NAL unit SHOULD, when possible, from an MTU size perspective, be encapsulated in an aggregation packet together with its associated BMCL NAL unit, as typically a non-BMCL NAL unit would be meaningless without the associated BMCL NAL unit being available.
 
 - 	For carrying exactly one NAL unit in an RTP packet, a single NAL unit packet MUST be used

The general concept behind de-packetization is to get the NAL units out of the RTP packets in an RTP stream and all RTP streams the RTP stream depends on, if any, and pass them to the decoder in the NAL unit decoding order.
The de-packetization process is implementation dependent. Therefore, the following de-packetization rules SHOULD be taken as an example.

 - 	All normal RTP mechanisms related to buffer management apply. In particular, duplicated or outdated RTP packets (as indicated by the RTP sequence number and the RTP timestamp) are removed. To determine the exact time for decoding, factors such as a possible intentional delay to allow for proper inter-stream synchronization must be factored in.
 
 - 	NAL units with NAL unit type values in the range of 0 to 44, inclusive, MAY be passed to the decoder. NAL-unit-like structures with NAL unit type values in the range of 45 to 63, inclusive, MUST NOT be passed to the decoder.
 
 - 	When sprop-max-don-diff is equal to 0 for the received RTP stream, the NAL units carried in the RTP stream MAY be directly passed to the decoder in their transmission order, which is identical to their decoding order.
 
 - 	When sprop-max-don-diff is greater than 0 for any of the received RTP streams, the received NAL units need to be arranged into decoding order before handing them over to the decoder.
 
 - 	For further de-packetization examples, the reader is referred to Section 6 of {{RFC7798}}.

Regarding the packetization of V3C video component data, the respective RTP video payload specification(s) define how packetization and de-packetization SHOULD be handled.


# Payload format for V-DMC Arithmetic-coded Displacement {#section6}

## General

This section describes details related to the RTP payload format definitions for the V-DMC arithmetic displacement codec defined in Annex J of {{ISO.IEC.23090-29}}. Aspects related to RTP header, RTP payload header and general payload structure are considered. As discussed in {{section4}}, a V-DMC stream may alternatively use another displacement codec, in which case  the RTP payload described in this section would not be applicable.


## RTP header Usage

The RTP header is defined in {{RFC3550}} and represented in {{figure-rtpheader2}}. Some of the header field values are interpreted as follows.

~~~~~~~~~~
   0                   1                   2                   3
    0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |V=2|P|X|  CC   |M|     PT      |       sequence number         |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |                           timestamp                           |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
   |           synchronization source (SSRC) identifier            |
   +=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+=+
   |            contributing source (CSRC) identifiers             |
   |                             ....                              |
   +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-rtpheader2 title="RTP header for V-DMC Displacement"}

   Marker bit (M): 1 bit

   Set for the last packet of the access unit, carried in the current
   RTP stream.  This is in line with the normal use of the M bit in
   video formats to allow an efficient playout buffer handling.

   Payload type (PT): 7 bits
   
   The assignment of a payload type MUST be performed either through the profile used or in a dynamic way.

   Sequence Number (SN): 16 bits

   Set and used in accordance with {{RFC3550}}
   
   Timestamp : 32 bits

   The RTP timestamp is set to the sampling timestamp of the content.  A
   90 kHz clock rate MUST be used.

   If the NAL unit has no timing properties of its own (e.g., 
   set and SEI NAL units), the RTP timestamp MUST be set to the RTP 
   timestamp of the coded displacement of the access unit in which the NAL
   unit (according to Section J of {{ISO.IEC.23090-29}} is included.

   Receivers MUST use the RTP timestamp for the display process, even
   when the bitstream contains displacement frame timing SEI messages as
   specified in {{ISO.IEC.23090-29}}.
   
   Synchronization source (SSRC): 32 bits 

   Used to identify the source of the RTP packets. By definition a
   single SSRC is used for all parts of a single bitstream. 
   The remaining RTP header fields are used as specified in {{RFC3550}}.

## RTP Payload header for AC-based Displacement {#displ-nal}
The RTP Payload Header follows the RTP header. {{figure-rtpheader-dp}} describes RTP Payload Header.

~~~~~~~~~~
  0                   1
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |F|    NUT    |    NLI    | TID |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-rtpheader-dp title="RTP header for AC-based Displacement."}

F : displ_nal_forbidden_zero_bit specified in {{ISO.IEC.23090-29}} MUST be equal to 0. 

NUT : displ_nal_unit_type as specified in {{ISO.IEC.23090-29}} define the type of the RBSP data structure contained in the NAL unit as specified in {{ISO.IEC.23090-29}}. In particular, the AC-based displacement NAL unit types supported are specified in Table J.1 of {{ ISO.IEC.23090-29}}.

NLI : displ_nal_layer_id as specified in {{ISO.IEC.23090-29}} defines the identifier of the layer to which an DCL NAL unit belongs or the identifier of a layer to which a non-DCL NAL unit applies. The value of displ_nal_layer_id shall be in the range of 0 to 62, inclusive. 

TID : displ_nal_temporal_id_plus1 minus 1 as specified in {{ISO.IEC.23090-29}} defines a temporal identifier for the NAL unit. The value of displ_nal_temporal_id_plus1 shall not be equal to 0.

## Payload structures

### General

Three different types of RTP packet payload structures are specified. A receiver can identify the payload structure by the first two bytes of the RTP packet payload, which co-serves as the RTP payload header. These two bytes are always structured as a NAL unit header. The NAL unit type field indicates which structure is present in the payload.

The three different payload structures are as follows:

Single NAL Unit Packet: Contains a single NAL unit in the payload. This payload structure is specified in {{displ-nalp}}

Aggregation Packet: Contains multiple NAL units in a single RTP payload. This payload structure is specified in {{disp-aggp}}.

Fragmentation Unit: Contains a subset of a single NAL unit. This payload structure is specified in {{displ-fragu}}.

NOTE: (informative) This memo does not limit the size of NAL units encapsulated in NAL unit packets and fragmentation units. {{ISO.IEC.23090-29}} does not restrict the maximum size of a NAL unit directly either. Instead, a NAL unit sample stream format may be used, which provides flexibility to signal NAL unit size up to UINT64_MAXUINT64_MAX bytes.


###	Single NAL unit packet {#displ-nalp}

Single NAL unit packet contains exactly one NAL unit, and consists of an RTP payload header and following conditional fields: 16-bit DONL and 16-bit vdmcd-id. The rest of the payload data contain the NAL unit payload data (excluding the NAL unit header). Single NAL unit packet MUST only contain atlas NAL units of the types defined in Table J.1 of {{ISO.IEC.23090-29}}. The structure of the single NAL unit packet is shown below in {{figure-singlenal-dp}}


~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |      RTP payload header       |      DONL (conditional)       |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |     vdmcd-id (cond)           |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
  |                                                               |
  |                        NAL unit data                          |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               :...OPTIONAL RTP padding        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-singlenal-dp title="Single NAL unit packet for AC-based Displacement"}


RTP payload header MUST be an exact copy of the NAL unit header of the contained NAL unit.

A NAL unit stream composed by de-packetizing single NAL unit packets in RTP sequence number order MUST conform to the NAL unit decoding order, when DONL is not present.

The DONL field, when present, specifies the value of the 16-bit decoding order number of the contained NAL unit. If sprop-max-don-diff is greater than 0 for any of the RTP streams, the DONL field MUST be present, and the variable DONL for the contained NAL unit is derived as equal to the value of the DONL field. Otherwise (sprop-max-don-diff is equal to 0 for all the RTP streams), the DONL field MUST NOT be present.

The vdmcd-id field, when present, specifies the 16-bit displacement identifier for the NAL unit, as signaled in displacement header defined in {{ISO.IEC.23090-29}}. If sprop-vdmcd-id-pres is equal to 1 and RTP payload header NUT is in range 0-29, inclusive, the vdmcd-id field MUST be present. Otherwise, the vdmcd-id field MUST NOT be present.

NOTE: (informative) Only values for NAL unit type (NUT) in range 0-29, inclusive, are allocated for AC-based displacement layer unit data in {{ISO.IEC.23090-29}}.

###	Aggregation packet {#disp-aggp}

Aggregation Packets (APs) enable the reduction of packetization overhead for small NAL units, such as most of the non-DCL NAL units, which are often only a few octets in size.

Aggregation packets MAY be used wrap multiple NAL units belonging to the same access unit in a single RTP payload. The first two bytes of the AP MUST contain RTP payload header. The NAL unit type (NUT) for the NAL unit header contained in the RTP payload header MUST be equal to 47, which falls in the unspecified range of the NAL unit types defined in {{ISO.IEC.23090-29}}. AP MAY contain a conditional vdmcd-id field. AP MUST contain two or more aggregation units. The structure of AP is shown in {{figure-aggrepacket-dp}}.


~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |  RTP payload header (NUT=47)  |         vdmcd-id (cond)       |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                                                               |
  |                  Two or more aggregation units                |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               :...OPTIONAL RTP padding        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-aggrepacket-dp title="Aggregation packet for AC-based displacement"}

The fields in the payload header are set as follows. The F bit MUST be equal to 0 if the F bit of each aggregated NAL unit is equal to zero; otherwise, it MUST be equal to 1. The NUT field MUST be equal to 47. The value of NLI MUST be equal to the lowest value of NLI of all the aggregated NAL units. The value of TID MUST be the lowest value of TID of all the aggregated NAL units.

All DCL NAL units in an aggregation packet have the same TID value since they belong to the same access unit. However, the packet MAY contain non-DCL NAL units for which the TID value in the NAL unit header MAY be different than the TID value of the DCL NAL units in the same AP.

The vdmcd-id field, when present, specifies the 16-bit displacement identifier for all DCL NAL units in the AP. If sprop-vdmcd-id-pres is equal to 1, the vdmcd-id field MUST be present. Otherwise, the vdmcd-id field MUST NOT be present.

AP MUST carry at least two aggregation units (AU) and can carry as many aggregation units as necessary. However, the total amount of data in an AP MUST fit into an IP packet, and the size SHOULD be chosen so that the resulting IP packet is smaller than the MTU size so to avoid IP layer fragmentation. The structure of the AU depends both on the presence of the decoding order number, the sequence order of the AU in the AP and the presence of vdmcd-id field. The structure of an AU is shown in {{figure-aggreunit-dp}}.

~~~~~~~~~~
   0                   1                   2                   3
   0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |  DOND (cond)  /  DONL (cond)  |        vdmcd-id (cond)        |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |            NALU size          |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               |
  |                                                               |
  |                            NAL unit                           |
  |                                                               |
  |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
  |                               |
  +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~~~~~~~~
{: #figure-aggreunit-dp title="Aggregation unit for AC-based Displacement"}


If sprop-max-don-diff is greater than 0 for any of the RTP streams, an AU begins with the DOND / DONL field. The first AU in the AP contains DONL field, which specifies the 16-bit value of the decoding order number of the aggregated NAL unit. The variable DON for the aggregated NAL unit is derived as equal to the value of the DONL field. All subsequent AUs in the AP MUST contain an (8-bit) DOND field, which specifies the difference between the decoding order number values of the current aggregated NAL unit and the preceding aggregated NAL unit in the same AP. The variable DON for the aggregated NAL unit is derived as equal to the DON of the preceding aggregated NAL unit in the same AP plus the value of the DOND field plus 1 modulo 65536.

When sprop-max-don-diff is equal to 0 for all the RTP streams, DOND / DONL fields MUST NOT be present in an aggregation unit. The aggregation units MUST be stored in the aggregation packet so that the decoding order of the containing NAL units is preserved. This means that the first aggregation unit in the aggregation packet SHOULD contain the NAL unit that SHOULD be decoded first.

If sprop-vdmcd-id-pres is equal to 2 and the AU NAL unit header type is in range 0-29, inclusive, the 16-bit vdmcd-id field MUST be present in the aggregation unit after the conditional DOND/DONL field. Otherwise vdmcd-id field MUST NOT be present in the aggregation unit.

The conditional fields of the aggregation unit are followed by a 16-bit NALU size field, which provides the size of the NAL unit (in bytes) in the aggregation unit. The remainder of the data in the aggregation unit SHOULD contain the NAL unit (including the unmodified NAL unit header).


### Fragmentation unit {#displ-fragu}

Fragmentation Units (FUs) are introduced to enable fragmenting a single NAL unit into multiple RTP packets, possibly without co-operation or knowledge of the encoder. A fragment of a NAL unit consists of an integer number of consecutive octets of that NAL unit. Fragments of the same NAL unit MUST be sent in consecutive order with ascending RTP sequence numbers (with no other RTP packets within the same RTP stream being sent between the first and last fragment.

When a NAL unit is fragmented and conveyed within FUs, it is referred to as a fragmented NAL unit. Aggregation packets MUST NOT be fragmented. FUs MUST NOT be nested; i.e., an FU MUST NOT contain a subset of another FU. The RTP header timestamp of an RTP packet carrying an FU is set to the NALU-time of the fragmented NAL unit.
A FU consists of an RTP payload header with NUT equal to a  63, an 8-bit FU header, a conditional 16-bit DONL field, a conditional 16-bit vdmc-id field and an FU payload. The structure of an FU is illustrated below in {{figure-frag-dp}}.
 
~~~~~~~~~~
  0                   1                   2                   3
  0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  RTP payload header (NUT=46)  |   FU header   |  DONL (cond)  |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |  DONL (cond)  |      vdmcd-id (cond)          |               |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+               |
 |                                                               |
 |                          FU payload                           |
 |                                                               |
 |                               +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 |                               :...OPTIONAL RTP padding        |
 +-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
 
~~~~~~~~~~
{: #figure-frag-dp title="Fragmentation Unit for Displacement"}

## Packetization and De-packetization Rules

The following packetization rules apply for V-DMC data:
 - 	If sprop-max-don-diff is greater than 0 for any of the RTP streams, the transmission order of NAL units carried in the RTP stream MAY be different than the NAL unit decoding order and the NAL unit output order. Otherwise (sprop-max-don-diff is equal to 0 for all the RTP streams), the transmission order of NAL units carried in the RTP stream MUST be the same as the NAL unit decoding order.
 - 	A NAL unit of a small size SHOULD be encapsulated in an aggregation packet together with one or more other NAL units in order to avoid the unnecessary packetization overhead for small NAL units. For example, non-DCL NAL units such as access unit delimiters, parameter sets, or SEI NAL units are typically small and can often be aggregated with DCL NAL units without violating MTU size constraints.
 - 	Each non-DCL NAL unit SHOULD, when possible, from an MTU size perspective, be encapsulated in an aggregation packet together with its associated DCL NAL unit, as typically a non-DCL NAL unit would be meaningless without the associated DCL NAL unit being available.
 - 	For carrying exactly one NAL unit in an RTP packet, a single NAL unit packet MUST be used

The general concept behind de-packetization is to get the NAL units out of the RTP packets in an RTP stream and all RTP streams the RTP stream depends on, if any, and pass them to the decoder in the NAL unit decoding order.

The de-packetization process is implementation dependent. Therefore, the following de-packetization rules SHOULD be taken as an example.

 - 	All normal RTP mechanisms related to buffer management apply. In particular, duplicated or outdated RTP packets (as indicated by the RTP sequence number and the RTP timestamp) are removed. To determine the exact time for decoding, factors such as a possible intentional delay to allow for proper inter-stream synchronization must be factored in.

 - 	NAL units with NAL unit type values in the range of 0 to 44, inclusive, MAY be passed to the decoder. NAL-unit-like structures with NAL unit type values in the range of 45 to 63, inclusive, MUST NOT be passed to the decoder.

 - 	When sprop-max-don-diff is equal to 0 for the received RTP stream, the NAL units carried in the RTP stream MAY be directly passed to the decoder in their transmission order, which is identical to their decoding order.

 - 	When sprop-max-don-diff is greater than 0 for any of the received RTP streams, the received NAL units need to be arranged into decoding order before handing them over to the decoder.

 - 	For further de-packetization examples, the reader is referred to Section 6 of {{RFC7798}}.

Regarding the packetization of V3C video component data, the respective RTP video payload specification(s) define how packetization and de-packetization SHOULD be handled.


# Payload Format Parameters

This section describes payload format optional parameters.  A mapping of the parameters into the Session Description Protocol (SDP) {{RFC8866}} is also provided for applications that use SDP. Equivalent parameters could be defined elsewhere for use with control protocols that do not use SDP.


## Media Type Registration for Basemesh {#media-type-bm}

The receiver MUST ignore any parameter unspecified in this memo. 

Type name: application 

Subtype name: bmesh

Required parameters : N/A

Optional parameters: bmesh-codec-idc, bmesh-level-idc, bmesh-lod, bmesh-seq-set, bmesh-tier-flag, bmesh-toolset-idc, sprop-bmesh-sei, sprop-bmesh-sm-id, sprop-bmesh-sm-id-pres

Optional parameters inherited from V3C: sprop-v3c-unit-header, sprop-v3c-unit-type, sprop-v3c-vps-id, sprop-v3c-atlas-id, sprop-v3c-attr-idx, sprop-v3c-attr-part-idx, sprop-v3c-map-idx, sprop-v3c-aux-video-flag, sprop-v3c-parameter-set, sprop-v3c-tile-id, sprop-v3c-tile-id-pres, sprop-v3c-atlas-data, sprop-v3c-common-atlas-data, sprop-v3c-sei, v3c-ptl-level-idc, v3c-ptl-tier-flag, v3c-ptl-codec-idc, v3c-ptl-toolset-idc, v3c-ptl-rec-idc and sprop-max-don-diff

Encoding considerations: This type is only defined for transfer via RTP {{RFC3550}}.

Security considerations: Please see {{security}}.

Interoperability considerations: N/A

Published specification: Please refer to {{ISO.IEC.23090-29}}

Applications that use this media type: Any application that relies on V-DMC-based media services over RTP.

Additional information: N/A

Person & email address to contact for further information:

Intended usage: COMMON

Restrictions on usage: N/A

Author: See Authors' Addresses section of this memo.

Change controller: IETF avtcore@ietf.org

Provisional registration? (standards tree only): No 


## Media Type Registration for Displacement {#media-type-displ}

The receiver MUST ignore any parameter unspecified in this memo. 

Type name: application

Subtype name: vdmcd

Required parameters : N/A

Optional parameters: vdmcd-codec-idc, vdmcd-level-idc, vdmcd-lod, vdmcd-seq-set,vdmcd-tier-flag, vdmcd-toolset-idc, sprop-vdmcd-sei, sprop-vdmcd-id,sprop-vdmcd-id-pres

Optional parameters inherited from V3C: sprop-v3c-unit-header, sprop-v3c-unit-type, sprop-v3c-vps-id, sprop-v3c-atlas-id, sprop-v3c-attr-idx, sprop-v3c-attr-part-idx, sprop-v3c-map-idx, sprop-v3c-aux-video-flag, sprop-v3c-parameter-set, sprop-v3c-tile-id, sprop-v3c-tile-id-pres, sprop-v3c-atlas-data, sprop-v3c-common-atlas-data, sprop-v3c-sei, v3c-ptl-level-idc, v3c-ptl-tier-flag, v3c-ptl-codec-idc, v3c-ptl-toolset-idc, v3c-ptl-rec-idc and sprop-max-don-diff

Encoding considerations: This type is only defined for transfer via RTP {{RFC3550}}.

Security considerations: Please see {{security}}.

Interoperability considerations: N/A

Published specification: Please refer to {{ISO.IEC.23090-29}}

Applications that use this media type: Any application that relies on V-DMC-based media services over RTP.

Additional information: N/A

Person & email address to contact for further information:

Intended usage: COMMON

Restrictions on usage: N/A

Author: See Authors' Addresses section of this memo.

Change controller: IETF avtcore@ietf.org

Provisional registration? (standards tree only): No 

## Optional Parameters Definition {#optional-param}

Optional parameters definition in section 7.2. of {{I-D.ietf-avtcore-rtp-v3c}} are applicable, unless updated in this section, which describes new and updated optional parameters definitions. 

**Parameters defined in {{I-D.ietf-avtcore-rtp-v3c}}**

The possible values of sprop-v3c-unit-type are updated by this memo. sprop-v3c-unit-type specifies a V3C unit type value corresponding to vuh_unit_type defined in {{ISO.IEC.23090-29}}, i.e., it defines V3C sub-bitstream type. {{ISO.IEC.23090-29}} only introduces new values and does not modify the values previously defined in {{ISO.IEC.23090-05}}. The remaining V3C parameters can also be utilized in the V-DMC bitstream if necessary.

**optional parameters for Basemesh are defined in this memo**

bmesh-codec-idc: 

bmesh-codec-idc indicates the codec group profile component to which the CVS conforms. It corresponds to bmptl_profile_codec_group_idc defined in {{ISO.IEC.23090-29}}.

bmesh-level-idc: 

bmesh-level-idc indicates a level to which the coded V3C sequence (CVS) conforms. It corresponds to bmptl_level_idc defined in {{ISO.IEC.23090-29}}.

bmesh-lod:

bmesh-lod specifies the support for signalling and adjusting the level of detail in the stream. bmesh-lod correspond to lod_index in {{ISO.IEC.23090-10}}.

bmesh-seq-set: 

bmesh-seq-set provides an identifier for the basemesh sequence parameter set for reference by other syntax elements defined in {{ISO.IEC.23090-29}}. It corresponds to bmsps_sequence_parameter_set_id defined in {{ISO.IEC.23090-29}}.

bmesh-tier-flag: 

bmesh-tier-flag specifies the tier context for the interpretation of bmptl_level_idc. It corresponds to bmptl_tier_flag defined in {{ISO.IEC.23090-29}}.

bmesh-toolset-idc: 

bmesh-toolset-idc indicates the toolset combination profile component to which the CVS conforms. It corresponds to bmptl_profile_toolset_idc defined in {{ISO.IEC.23090-29}}.

sprop-bmesh-sei: 

sprop-bmesh-sei MAY be used to convey SEI NAL units of VDMC basemesh sub bitstreams for out-of-band transmission. The value is defined in Table H.1 of {{ISO.IEC.23090-29}}. 

sprop-bmesh-sm-id: 

sprop-bmesh-id indicates that the RTP stream contains only portion of the frame in the basemesh. sprop-bmesh-id is a comma-separated (',') list of integer values, which indicate the sprop-bmesh-ids that are present in the RTP stream.

sprop-bmesh-sm-id-pres: 

sprop-bmesh-sm-id-pres indicates that the RTP packets contain bmesh-sm-id field.


**optional parameters for AC-based displacement are defined in this memo**

vdmcd-codec-idc: 

vdmcd-codec-idc indicates the codec group profile component to which the CDS conforms as specified in Annex J of {{ISO.IEC.23090-29}}. It corresponds to dptl_profile_codec_group_idc defined in {{ISO.IEC.23090-29}}.

vdmcd-level-idc: 

vdmcd-level-idc indicates a level to which the CDS conforms as specified in Annex J of {{ISO.IEC.23090-29}}. The vdmcd-level-idc parameter corresponds to dptl_level_idc defined in {{ISO.IEC.23090-29}}.

vdmcd-lod:

vdmcd-lod specifies the support for signalling and adjusting the level of detail in the stream. vdmcd-lod correspond to lod_index in {{ISO.IEC.23090-10}}.

vdmcd-seq-set: 

vdmcd-seq-set provides an identifier for the base mesh sequence parameter set for reference by other syntax elements defined in {{ISO.IEC.23090-29}}. It corresponds to dsps_sequence_parameter_set_id defined in {{ISO.IEC.23090-29}}.


vdmcd-tier-flag: 

vdmcd-tier-flag specifies the tier context for the interpretation of dptl_level_idc as specified in Annex J of {{ISO.IEC.23090-29}}. It corresponds to dptl_tier_flag defined in {{ISO.IEC.23090-29}}.


vdmcd-toolset-idc: 

vdmcd-toolset-idc indicates the toolset combination profile component to which the CDS conforms as specified in Annex J of {{ISO.IEC.23090-29}}. It corresponds to dptl_profile_toolset_idc defined in {{ISO.IEC.23090-29}}.

sprop-vdmcd-sei:

sprop-vdmcd-sei MAY be used to convey SEI NAL units of VDMC displacement sub bitstreams for out-of-band transmission. The value is defined in Table J.1 of {{ISO.IEC.23090-29}}. 

sprop-vdmcd-id:

sprop-vdmcd-id indicates that the RTP stream contains only portion of the frame in the displacement. sprop-vdmcd-id is a comma-separated (',') list of integer values, which indicate the sprop-vdmcd-ids that are present in the RTP stream.

sprop-vdmcd-id-pres:

sprop-vdmcd-id-pres indicates that the RTP packets contain vdmcd-id field.



# Congestion control considerations

In a case of congestion, adjusting the LoD level of each V-DMC stream can help partially mitigate the congestion issue. The substreams that make up V-DMC can independently adjust their LoD (Level of Detail) levels with high level parameters (e.g, bmesh-lod, vdmcd-lod). This parameter is linked to LoD-related parameters defined for each substream, allowing the overall LoD to be fine-tuned. During periods of congestion, a higher LoD level may overload transmitters and/or receivers, while also increasing network bandwidth consumption. To alleviate this, the LoD levels of individual substreams can be adjusted to reduce overall bandwidth usage and improve processing speed. 


# Session description protocol {#session-descp}

This document complies with, and builds over, SDP mechanisms defined in {{I-D.ietf-avtcore-rtp-v3c}}, including the "v3cfmtp" attribute and the grouping framework "v3c" type.

##	V3C format parameters "v3cfmtp" attribute
The "v3cfmtp" SDP attribute is used to carry V3C specific media format parameters, which were defined in {{ISO.IEC.23090-05}}. This memo defines new values for one of these parameters, v3c-unit-type. New SDP parameters defined in this memo are carried with the "fmtp" attribute.


##	Mapping of Payload Type Parameters to SDP

###	For V-DMC Basemesh Components

The mapping of above defined payload format media type to the corresponding fields in the Session Description Protocol (SDP) is done according to {{RFC8866}}.

   *  The media name in the "m=" line of SDP MUST be application.

   *  The encoding name in the "a=rtpmap" line of SDP MUST be bmesh

   *  The clock rate in the "a=rtpmap" line MUST be 90000.

   *  The OPTIONAL parameters , when present, MUST be included in the "a=fmtp" line of SDP.  This is expressed as a media type string, in the form of a semicolon-separated list of parameter=value pairs.
   *  The OPTIONAL parameters bmesh-codec-idc, bmesh-level-idc, bmesh-lod, bmesh-seq-set, bmesh-tier-flag, bmesh-toolset-idc, sprop-bmesh-sei, sprop-bmesh-sm-id, sprop-bmesh-sm-id-pres when present, MUST be included in the "a=fmtp" line of SDP. This parameter is expressed as a media type string, in the form of a semicolon-separated list of parameter=value pairs.   
   
The OPTIONAL parameters, when present in the basemesh component media line format parameters attribute, specify values that are valid for the coded V3C sequence until a new value is received in-band. Some OPTIONAL parameters, like sprop-v3c-parameter-set or sprop-v3c-unit-header, can't be carried in-band in the basemesh stream and may thus be considered static for the session. The carriage of basemesh payload format parameters in "a=fmtp" and "a=v3cfmtp" attributes is separated by logical context, where "a=fmtp" consists of basemesh level media format parameters and "a=v3cfmtp" contains V3C level media format parameters.


An example of media representation corresponding to the vdmc RTP payload in SDP is as follows:

~~~~~~~~~~
   m=application 49170 RTP/AVP 98
   a=rtpmap:98 bmesh/90000
   a=fmtp:98 sprop-bmesh-sm-id=0,1
   a=v3cfmtp:sprop-v3c-unit-header=OAAAAA==;
~~~~~~~~~~

### For V-DMC Displacement Components

   *  The media name in the "m=" line of SDP MUST be application.

   *  The encoding name in the "a=rtpmap" line of SDP MUST be vmdcd

   *  The clock rate in the "a=rtpmap" line MUST be 90000.


   *  The OPTIONAL parameters, when present, MUST be included in the "a=fmtp" line of SDP.  This is expressed as a media type string, in the form of a semicolon-separated list of parameter=value pairs.
   *  The OPTIONAL parameters vdmcd-codec-idc, vdmcd-level-idc, vdmcd-lod, vdmcd-seq-set,vdmcd-tier-flag, vdmcd-toolset-idc, sprop-vdmcd-sei, sprop-vdmcd-id,sprop-vdmcd-id-pres when present, MUST be included in the "a=fmtp" line of SDP. This parameter is expressed as a media type string, in the form of a semicolon-separated list of parameter=value pairs.
   
The OPTIONAL parameters, when present in the displacement component media line format parameters attribute, specify values that are valid for the coded displacement sequence until a new value is received in-band. Some OPTIONAL parameters, like sprop-v3c-parameter-set or sprop-v3c-unit-header, can't be carried in-band in the displacement stream and may thus be considered static for the session. The carriage of V3C payload format parameters in "a=fmtp" and "a=v3cfmtp" attributes is separated by logical context, where "a=fmtp" consists of displacement level media format parameters and "a=v3cfmtp" contains V3C level media format parameters.


An example of media representation corresponding to the vdmc RTP payload in SDP is as follows:

~~~~~~~~~~
   m=application 49170 RTP/AVP 98
   a=rtpmap:98 vdmcd/90000
   a=fmtp:98 sprop-vdmcd-id=0,1
   a=v3cfmtp:sprop-v3c-unit-header=GAAAABB==;
   
~~~~~~~~~~

### For Other V-DMC Components

The mapping of payload type parameters to SDP is described in section 9.2.1. of {{I-D.ietf-avtcore-rtp-v3c}} for the V3C atlas components and in section 9.2.2. of {{I-D.ietf-avtcore-rtp-v3c}} for other video V-DMC components.


### Grouping Framework {#grouping}

The grouping framework described in section 9.3. of {{I-D.ietf-avtcore-rtp-v3c}} can be used for V-DMC. Group attribute with VDMC type is provided to allow application to identify "m" lines that belong to the same VDMC bitstream. Grouping type VDMC MUST be used with the group attribute. The tokens that follow are mapped to 'mid'-values of individual media lines in the SDP. 

~~~~~~~~~~
 a=group:VDMC <tokens>
~~~~~~~~~~

The following example shows an SDP including four media lines, three describing VDMC components (PT:96=attribute, PT:97=displacement, PT:98=basemesh) and one VDMC atlas component (PT:100). All the media lines are grouped under one VDMC group. V3C parameter set is provided via session level VDMC media format parameter attribute.

~~~~~~~~~~
  ...
a=group:VDMC 1 2 3 4 
m=video 40000 RTP/AVP 96
a=rtpmap:96 H264/90000 
a=fmtp:96 
  v3c-unit-header=EAAAAA==;
a=mid:1
m=application 40002 RTP/AVP 97
a=rtpmap:97 disp/90000 
a=fmtp:97 
  v3c-unit-header=GAAAABB==; 
a=mid:2
m=application 40004 RTP/AVP 98
a=rtpmap:98 bmesh/90000  
a=fmtp:98 
  v3c-unit-header=IAAAAA==;
a=mid:3
m=application 40008 RTP/AVP 100
a=rtpmap:100 v3c/90000  
a=fmtp:100
  v3c-unit-header=CAAAAA==; 


~~~~~~~~~~


## Offer and Answer Considerations
An example offer, which allows bundling different V-DMC components (attribute, basemesh, AC-based displacement, atlas) into one stream, based on {{RFC9143}}. 

~~~~~~~~~~
  ...
  a=group:BUNDLE 1 2 3 4
  a=group:VDMC 1 2 3 4
  m=video 40000 RTP/AVP 96
  a=rtpmap:96 H264/90000
  a=v3cfmtp:sprop-v3c-unit-type=4;sprop-v3c-vps-id=0;
  sprop-v3c-atlas-id=0
  a=mid:1
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 40002 RTP/AVP 97
  a=rtpmap:97 bmesh /90000
  a=v3cfmtp:sprop-v3c-unit-type=7;sprop-v3c-vps-id=0;
  sprop-v3c-atlas-id=0
  a=mid:2
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 40004 RTP/AVP 98
  a=rtpmap:98 vdmcd /90000
  a=v3cfmtp:sprop-v3c-unit-type=8;sprop-v3c-vps-id=0;
  sprop-v3c-atlas-id=0
  a=mid:3
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 40006 RTP/AVP 99
  a=rtpmap:99 v3c/90000
  a=v3cfmtp:sprop-v3c-unit-type=1;sprop-v3c-vps-id=0;
  sprop-v3c-atlas-id=0;
  sprop-v3c-parameter-set=AQD/AAAP/zwAAAAAADwIAQ5BwAAOADjgQAADkA==
  a=mid:4
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  
~~~~~~~~~~

An example answer, which accepts bundling of different V-DMC components.


~~~~~~~~~~
  a=group:BUNDLE 1 2 3 4
  a=group:VDMC 1 2 3 4
  m=video 50000 RTP/AVP 96
  a=rtpmap:96 H264/90000
  a=mid:1
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 0 RTP/AVP 97
  a=rtpmap:97 bmesh/90000
  a=bundle-only
  a=mid:2
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 0 RTP/AVP 98
  a=rtpmap:98 vdmcd/90000
  a=bundle-only
  a=mid:3
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid
  m=application 0 RTP/AVP 99
  a=rtpmap:99 v3c/90000
  a=bundle-only
  a=mid:4
  a=extmap:1 urn:ietf:params:rtp-hdrext:sdes:mid

~~~~~~~~~~



## Declarative SDP Considerations

When V-DMC content is offered over RTP in a declarative style, the considerations in section 9.5. of {{I-D.ietf-avtcore-rtp-v3c}} are applicable.

# IANA Considerations

## VDMC media type registration
New media types will be registered with IANA; see {{media-type-bm}} and {{media-type-displ}}.

## VDMC grouping type extension

Grouping is extended to establish relationships between substreams of a VDMC representation. A new group type (VDMC) for the group attribute will be registered as defined in {{grouping}}.  This document registers the semantics in {{table-group-id}} with IANA in the "Semantics for the 'group' SDP Attribute" registry group (under the "Session Description Protocol (SDP) Parameters" registry):

~~~~~~~~~~ table
+==============+=======+==============+=============+
| Semantics    | Token | Mux Category | Reference   |
+==============+=======+==============+=============+
|VDMC grouping | VDMC  |   NORMAL     | "this memo" |
+--------------+-------+--------------+-------------+
~~~~~~~~~~ 
{: #table-group-id title="Additional semantics for VDMC SDP group type"}

   NOTE: (informative) "this memo" to be replaced with the RFC number,
   once it becomes available.


# Security Considerations {#security}

RTP packets using the payload format defined in this specification are subject to the security considerations discussed in the RTP specification {{RFC3550}}, and in any applicable RTP profile such as RTP/AVP {{RFC3551}}, RTP/AVPF {{RFC4585}}, RTP/SAVP {{RFC3711}}, or RTP/SAVPF [RFC5124]. However, as "Securing the RTP Protocol Framework: Why RTP Does Not Mandate a Single Media Security Solution" [RFC7202] discusses, it is not an RTP payload format's responsibility to discuss or mandate what solutions are used to meet the basic security goals like confidentiality, integrity, and source authenticity for RTP in general. This responsibility lays on anyone using RTP in an application. They can find guidance on available security mechanisms and important considerations in "Options for Securing RTP Sessions" [RFC7201]. Applications SHOULD use one or more appropriate strong security mechanisms. The rest of this Security Considerations section discusses the security impacting properties of the payload format itself.

# Acknowledgments

Srinivas Gudumasu(InterDigital Canada) and Gurdeep Singh Bhullar and Olivier Mocquard(InterDigital Europe Ltd.) contributed to this draft as co-authors.

Thanks to Chandok Gurtej Singh, Harald Tveit Alvestrand, Mo Zanaty, Lukasz Kondrad and Danillo Bracco Graziosi for discussions and comments about this draft.