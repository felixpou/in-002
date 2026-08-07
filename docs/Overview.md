# Time & Identity in the Dynamic Media Facility

**A Briefing on Timing Principles, Identity Rules & Future Work**

## Why Timing and Identity Matter in a DMF

Inside a Dynamic Media Facility (DMF), media processing is fully asynchronous. Functions read and process data as soon as it is available, without the frame-locked discipline of a traditional SDI/genlocked environment. This removes unnecessary buffering delays and enables faster-than-real-time operations, but it means that every piece of media must carry its own time context. Without it, there is no way to align audio to video, mix multiple cameras, record reliably, or hand off to downstream synchronous systems.

> **KEY PRINCIPLE**  
> The preservation of timing relationships of media signals throughout a live production workflow is the foundation of a functioning DMF.

The DMF Reference Architecture (EBU White Paper v2.0, April 2026) and this document together define how this is achieved through two inseparable concepts: timestamps carried on every grain of media, and stable, unique identities for every Source and Flow in the system. This foundational content identity distinguishes between editorial intent and technical representation, ensuring that media transformations are tracked accurately across the entire production workflow.

## Core Definitions

The DMF builds on the concepts and terminology of the JT-NM and JT-DMF Reference Architectures.

| Term           | Definition                                                                                                                                                                                                                   |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Source         | Editorial descriptor for media essence on a timeline. Each Source has a unique, immutable identity. A Source is realised by one or more Flows, each of which represents the editorial intent in a specific technical format. |
| Flow           | Technical representation of a Source (for example, a sequence of video frames or audio samples). Each Flow has a unique, immutable identity.                                                                                 |
| Grain          | The smallest directly indexable item in a Flow. One video frame equals one Grain. One audio sample per channel equals one Grain.                                                                                             |
| Media Workload | Inherited directly from the DMF Reference Architecture.                                                                                                                                                                      |

## Goal of Streamlined Approach

This document presents a streamlined methodology for Media Functions to execute inter-essence time alignment. The aim is to promote seamless interoperability across diverse vendor implementations.

## Phase 1 Key Principles (June 2026)

The JT-DMF E2E Synchronisation working document defines a phased approach to end-to-end timing across a complete hybrid production chain. Phase 1 addresses timing within the DMF media workload itself. Future phases will address ingress conforming and upstream signal chains.

> **SCOPE NOTE**  
> Phase 1 covers the asynchronous domain inside a DMF workload. Conforming of external sources and upstream timing are explicitly deferred to Phase 2 and beyond.

### Timing Relationship Preservation

The preservation of timing relationships of media signals throughout a live production workflow is the foundation of a functioning DMF.

### Asynchronous by Design

Software-defined media in the DMF unlocks new possibilities, allowing processing to proceed asynchronously without re-syncing at every stage. As defined in the DMF Reference Architecture, each individual essence is carried discretely. Re-alignment is deferred until essences need to be mixed together or multiplexed for presentation.

### Time Is Used as an Index

Time alignment is achieved by matching timestamps on Grains, not by genlocking or frame-synchronising Media Functions.

### A Single Common Time Domain Underpins Each DMF Media Workload

All Grains must carry timestamps coherent with this domain to facilitate asynchronous media processing and downstream alignment.

### The ITS (Indexing Time Stamp) Is the Canonical Timing Value Within the Workload

ITS is the intended timing value assigned to each Grain within a DMF workload. It is used as the index for all alignment and selection operations. ITS values are chosen to phase align media units (video frames or audio samples) to the SMPTE Epoch. Indexing Time Stamps are expressed as nanoseconds from the SMPTE Epoch (00:00, 1 January 1970).

### Timestamps Are Propagated Through Media Functions

When a Media Function processes a Grain from a Flow, the ITS of the input Grain is carried through to the output Grain.

### Technical Modifications Create a New Flow, Same Source

If a Media Function changes only the technical representation without changing the editorial intent, such as encoding or sample format conversion, the Source identity is preserved and only the Flow identity changes.

This separates differences in technical format and quality, such as main and proxy versions, from editorial differences. For example, an encoded video stream delivered at multiple bitrates in an Adaptive Bit Rate (ABR) ladder would produce multiple Flows of the same Source.

### Editorial Modifications Create a New Source and New Flow

If a Media Function changes the editorial meaning of the media, such as colour grading, audio EQ, time shifting, or mixing multiple inputs, the resulting Flow receives both a new Source identity and a new Flow identity.

This allows editorial changes to be tracked independently from purely technical changes.

## Key Rules: How Time and Identity Are Assigned and Propagated

### All External Signals Must Be Conformed to the Common Media Workload Time Domain on Ingress

This conforming process is imperative and will be defined fully in a future phase of work.

### Critical Timing Relationships Between Signals Must Be Preserved and Tracked

Some Flows share a timing relationship, such as video and audio from the same scene. Any Media Function performing alignment or mixing must be able to discover these relationships. The method for achieving this will be defined in a later phase of work.

### Recording a Flow in the DMF Domain Preserves Its Identity and Timestamps

When recording, Flows are written independently as mono-essence with their identity and timing metadata intact. This differs from the conventional approach of aligning and multiplexing media before recording.

### Generated or Replayed Sources Are Conformed to the Live Context

For generated or replayed Flows, Grain timestamps are synthesised to match the DMF time context into which they are injected, creating a new Source and a new Flow. Any inter-essence alignment relationship may need to be maintained on replay.

## Future Work

### Ingress Conforming (JT-DMF Priority)

How external synchronous signals, including cameras, contribution streams, SDI, and SMPTE ST 2110, are conformed to the DMF common time domain requires further definition. This includes how intentional offsets are calculated, logged, and carried either dynamically on MXL interfaces or statically in a metadata store.

### Upstream Signal Chain Timing

For a fully end-to-end solution, timing relationships must be captured at the point of acquisition, such as the camera shutter or microphone capsule. Few protocols currently allow this information to be propagated accurately, so additional work outside JT-DMF will be required.

### Timing Relationship Propagation

Timing relationships between Flows must be propagated through the DMF media workload. The required mechanism or mechanisms remain to be defined.

### Multi-Cluster Timing

Media exchange and timing between clusters, including maintaining asynchronous operation across WAN-connected sites, remain subjects for further study.
