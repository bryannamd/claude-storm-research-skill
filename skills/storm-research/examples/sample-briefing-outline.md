# Sample Briefing Outline

This shows how the **terminal output** can be organized around the topic — with an executive summary, a table of contents, and topical chapters like the ones below.

Note that the terminal output is free to use topical chapters (as shown here), but the final report always follows the fixed six-section template in `docs/output-schema.md` (60-second summary → findings ranked by reliability → disagreements → assumption & the missing expert → actions → badged sources). This example is a terminal output outline.

## Edge AI in Passenger Autonomous Vehicles

**Executive Summary**
A high-level overview of the shift toward local inference. It highlights the critical need for sub-millisecond latency in passenger safety systems.

**Table of Contents**
Links to all major sections for easy navigation.

**1. The Latency Imperative**
Explains why cloud round-trips fail at highway speeds. Details the physical limits of 5G networks in rural areas.

**2. Hardware Accelerators: TPUs vs NPUs**
Compares current silicon solutions. Breaks down power consumption and thermal management inside the vehicle chassis.

**3. Sensor Fusion at the Edge**
How local AI processes LiDAR, radar, and optical data simultaneously without overwhelming the main CPU.

**Sources**
* (Sample/Illustrative) Chen, L. et al. (2025). "Sub-millisecond Inference in Automotive NPUs." IEEE Transactions on Intelligent Vehicles.
* (Sample/Illustrative) Tesla AI Day 2024 Technical Whitepaper.
* (Sample/Illustrative) NVIDIA Drive Thor Architecture Reference Guide.
* (Sample/Illustrative) Waymo Open Dataset Analysis Report (2025 Edition).

**Appendix**
Raw benchmark tables and glossary of hardware terms.
