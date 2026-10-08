---
id: WD01
title: Weak-Area Drill — Troubleshooting Methodology
course: CompTIA Network+
covers: L21 (+ L22)
date: 2026-10-08
status: Not attempted
questions: 12
source: Original drill targeting NQ06 misses (Q11, Q13, Q16)
---

# WD01 — Weak-Area Drill: Troubleshooting Methodology

> Targets the three NQ06 misses: **verify vs establish-theory** (Q13), **step 1 sub-tasks** (Q16), and **NIC upgrade → cable type** (Q11). For each question, name the step *and* why — the same action (checking OSI layers) can belong to different steps depending on whether you're **finding the cause** or **confirming the fix**.

**Order to know cold:** Identify → Establish theory → Test theory → Plan of action → Implement/escalate → Verify + preventive → Document

---

## Questions

**1.** After applying a fix, a technician confirms the link light is on, the client has a valid IP, DNS resolves, and the application loads. Which step is this?

- A) Establish a theory of probable cause
- B) Identify the problem
- C) Test the theory to determine the cause
- D) Verify full system functionality

**2.** Before any fix, a technician checks the cable, then the IP configuration, then DNS, then the application, looking for the cause. Which step is this?

- A) Establish a theory of probable cause (bottom-to-top approach)
- B) Document findings
- C) Implement the solution
- D) Verify full system functionality

**3.** A technician reviews syslog for configuration changes made in the last 24 hours. Which step is this?

- A) Identify the problem
- B) Test the theory
- C) Establish a plan of action
- D) Verify full system functionality

**4.** A technician logs into a second workstation in the same room to see whether the same error occurs there. Which step is this?

- A) Verify full system functionality
- B) Implement the solution
- C) Identify the problem (duplicate the problem)
- D) Test the theory

**5.** Before changing a core switch's configuration, a technician writes down the rollback steps and lists the services that depend on that switch. Which step is this?

- A) Test the theory
- B) Document findings
- C) Implement the solution
- D) Establish a plan of action and identify potential effects

**6.** After resolving a DHCP exhaustion outage and confirming users have connectivity, a technician adds an alert at 80% scope utilization. Which step does the alert belong to?

- A) Document findings
- B) Establish a plan of action
- C) Verify full system functionality and implement preventive measures
- D) Identify the problem

**7.** A technician updates the ticket with the root cause, the configuration change made, and a note to update the switch template. Which step is this?

- A) Implement the solution
- B) Verify full system functionality
- C) Document findings, actions, outcomes, and lessons learned
- D) Establish a plan of action

**8.** A user reports that email won't sync and that a mapped drive is missing. According to the methodology, how should the technician proceed?

- A) Approach multiple problems individually
- B) Skip to implementing a reboot
- C) Assume a single root cause and fix it
- D) Escalate both immediately

**9.** A technician first checks that the PC is powered on and the cable is seated before investigating routing. Which part of the methodology is this?

- A) Duplicate the problem (identify)
- B) Question the obvious (establish a theory)
- C) Verify functionality
- D) Implement preventive measures

**10.** Which step comes immediately BEFORE implementing the solution or escalating?

- A) Identify the problem
- B) Test the theory to determine the cause
- C) Establish a plan of action to resolve the problem and identify potential effects
- D) Verify full system functionality

**11.** Suspecting DNS, a technician pings a server by IP (success) and then by hostname (failure). Which step is this?

- A) Verify full system functionality
- B) Test the theory to determine the cause
- C) Identify the problem
- D) Establish a plan of action

**12.** A server's 1 Gbps NIC was replaced with a 10GBASE-T NIC to improve performance. Afterwards the link won't come up at 10 Gbps over the existing Cat5e run. What is the most likely cause?

- A) PoE budget exceeded
- B) Port security violation
- C) Incorrect cable type for the new interface
- D) DNS misconfiguration

---

## Answer Key

| # | Ans | Explanation |
|---|-----|-------------|
| 1 | **D** | Checking that every layer works AFTER the fix = verification (this was the NQ06 Q13 trap). |
| 2 | **A** | Working through layers to FIND the cause = step 2 approach. Compare with the previous question: same checks, different purpose. |
| 3 | **A** | Gathering information / determining what changed = step 1. |
| 4 | **C** | Duplicating the problem is a step 1 sub-task. |
| 5 | **D** | Rollback and impact analysis = step 4. |
| 6 | **C** | Preventive measures are grouped with verification (step 6). |
| 7 | **C** | Documentation is the final step. |
| 8 | **A** | Treat multiple problems separately until they're proven related. |
| 9 | **B** | 'Question the obvious' is under Establish a theory. |
| 10 | **C** | Order: … test → plan → implement → … |
| 11 | **B** | Running a check to confirm/deny a specific suspicion = testing the theory. |
| 12 | **C** | 10GBASE-T needs Cat6 (≤55 m) or Cat6a (100 m). After a NIC upgrade, check cable type first (NQ06 Q11 lesson). |
