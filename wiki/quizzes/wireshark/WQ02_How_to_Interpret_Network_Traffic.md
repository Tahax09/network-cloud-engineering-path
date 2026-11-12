---
id: WQ02
title: How to Interpret Network Traffic Quiz (Coursera graded)
course: Wireshark 101 - Packet Analysis Essentials
covers: WS11–WS16 (Module 3)
date: 2026-11-12
status: Answered (pre-submission)
score: pending
questions: 5
---

# WQ02 — How to Interpret Network Traffic Quiz

> Answered before grading, at Tah's request. Confirm against the graded result.

1. How can you turn off the default coloring rules in Wireshark?
   - A) By clicking on the toolbar and removing the coloring rules · B) By adjusting the screen layout in the settings · C) By adding a custom column in the profile settings · D) By refreshing the PCAP file after changing the profile
2. Why would you add a specific value as a column in Wireshark?
   - A) To minimize the number of columns · B) To increase Wireshark's processing speed · C) To highlight important information for quick analysis · D) To create custom protocols for packet analysis
3. Why are Wireshark profiles useful for network analysts?
   - A) They allow customization of TCP behavior in network traffic · B) They enable faster analysis by highlighting essential packets · C) They automatically adjust the seating and mirrors in a car · D) They provide different color schemes for the Wireshark interface
4. How can you add a custom column in Wireshark?
   - A) Right-click on the packet and select "Add Column" · B) Go to the Tools menu and choose "Add Custom Column" · C) Access the preferences or settings and navigate to the columns section · D) Use the shortcut Ctrl + Shift + C to create a new column
5. What is the purpose of adding a time column in Wireshark?
   - A) To customize login settings · B) To display the current time in Wireshark · C) To show the time of creation for each packet · D) To highlight the arrival time differences between packets

## Study pointers
| Q | Lesson |
|---|--------|
| 1 | [[WS07 - Coloring Traffic]] |
| 2, 4, 5 | [[WS06 - Configuring Columns]] |
| 3 | [[WS08 - Creating Profiles]] |

## Answer Key
| # | Answer | Why |
|---|--------|-----|
| 1 | **A** | The **Colorize Packet List** toolbar button (View → Colorize Packet List) toggles coloring rules off and on ([[WS07 - Coloring Traffic]]) |
| 2 | **C** | A column puts one field from every packet in the list, so you can see and sort it fast (e.g. Delta time, TTL) |
| 3 | **B** | Profiles bundle columns, coloring rules and filter buttons for a task, so the important packets stand out faster. D is only a small part of it |
| 4 | **C** | Preferences → Appearance → Columns → + (Method 1 in [[WS06 - Configuring Columns]]). A is wrong as worded: you right-click a **field** and choose **Apply as Column**, not "Add Column" on a packet |
| 5 | **D** | A delta-time column shows the gap between packets, so sorting it finds the biggest delays |
