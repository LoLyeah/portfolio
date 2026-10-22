---
title: "Anesthesia"
summary: "An all-in-one, zero-latency clinical decision support & acute care dashboard for healthcare professionals across all specialties. 100% offline-capable PWA benchmarked against MDCalc, Epocrates, ASRA Coags, and POCUS ultrasound stations."
date: "Oct 22 2026"
draft: false
tags:
- "v2.4.0"
- TypeScript
- Next.js 16 (Turbopack)
- PWA / Offline IndexedDB
- POCUS Ultrasound
- Clinical Decision Support
- Medical Tech
repoUrl: https://github.com/rizqihan/Anesthesia
---

**Anesthesia** (v2.4.0 — *Clinical Precision & Emergency Resilience*) is a universal, zero-latency clinical decision support and point-of-care medical suite for physicians, residents, intensivists, and healthcare professionals across all specialties.

Built with **Next.js 16 (App Router with Turbopack)**, **Tailwind CSS 4**, and **Dexie IndexedDB**, it unifies disparate bedside tools—traditionally fragmented across MDCalc, Epocrates, ASRA Coags, UpToDate, and ultrasound carts—into a single, privacy-first, **100% offline-capable Progressive Web Application (PWA)** that runs directly in the browser with zero cloud round-trips.

---

### Core Clinical Pillars & Capabilities

#### 1. 🧮 Universal Clinical Risk Scores & Lab Interpreters *(MDCalc Benchmark)*
* **Cardiovascular & VTE:** Wells Score for DVT & Pulmonary Embolism (with integrated PERC rule-out), CHA₂DS₂-VASc (annual stroke risk & anticoagulation threshold), HAS-BLED (anticoagulation bleeding risk), and HEART Score (emergency department chest pain & 6-week MACE stratification).
* **Critical Care & Sepsis:** CURB-65 & Pneumonia Severity Index (PSI), qSOFA bedside screening & full 6-organ SOFA trajectory scoring.
* **Neurology & Stroke:** Glasgow Coma Scale (GCS & GCS-P with pupil reactivity), NIH Stroke Scale (NIHSS) with IV Tenecteplase / Alteplase thrombolysis contraindication checklist.
* **Hepatology & Nephrology:** MELD-Na (UNOS-capped bounds) and Child-Pugh surgical mortality risk; race-free CKD-EPI 2021 eGFR equation with anesthetic drug handling recommendations.
* **Gastroenterology & Hemodynamics:** Glasgow-Blatchford Bleeding Score (GBS) for upper GI hemorrhage triage; Shock Index (SI) & Modified Shock Index (MSI) to detect occult hypoperfusion before overt hypotension.
* **Point-of-Care Lab Engines:** 4-Step ABG Analyzer (with Winter's compensation & delta-delta ratio for mixed disorders), Katz/Hillier hyperglycemia-corrected sodium, and potassium repletion boundaries.

#### 2. 📡 Point-of-Care Ultrasound (POCUS) Diagnostic Station
* **Interactive 36-Case Reference Atlas:** Pre-cached cine-loops across eFAST (RUQ Morison's pouch, LUQ splenorenal, suprapubic, subxiphoid pericardium, thoracic sliding), Lung BLUE protocol (Bat sign, A/B-lines, lung point, shred sign), Focused Cardiac Ultrasound (PLAX, PSAX, A4C, IVC long axis), RUSH shock protocol, and Lower Extremity DVT 2-point compression.
* **2D SVG Anatomical Mannequin:** Interactive touch/pointer tracking with acoustic window lock, probe orientation marker, and real-time rotation dial ($0^\circ\text{–}90^\circ$).
* **Live Hardware Feed & DICOM Review:** Direct UVC video capture (`getUserMedia`) connecting ultrasound carts via HDMI-to-USB cards; on-canvas 2-point distance calipers, elliptical area measurement, 3-axis automated bladder volume, real-time double-buffered M-mode column extractor, and client-side offline DICOM parser (`dicom-parser`).

#### 3. 🛡️ ASRA Coags Regional Anticoagulation Matrix *(ASRA Benchmark)*
* American Society of Regional Anesthesia (ASRA 5th Consensus) guidelines for spinal, epidural, deep plexus, and superficial peripheral nerve blocks.
* Washout hold times, catheter pull intervals, and post-procedure restart schedules across 14 antithrombotic drugs (LMWH, UFH, Apixaban, Rivaroxaban, Dabigatran with CrCl adjustments, Warfarin with target $\text{INR} \le 1.4$, P2Y12 inhibitors, and Aspirin).

#### 4. ⚡ Vasoactive Syringe Pump Infusion Titrator (50 mL) *(Vargo Benchmark)*
* Continuous infusion syringe titrator with 10 gold-standard ICU/OR protocols (Norepinephrine, Epinephrine, Vasopressin, Phenylephrine, Nicardipine, Propofol 1%/2% TIVA, Remifentanil TIVA, Dexmedetomidine, Dopamine, Dobutamine).
* Real-time bidirectional rate converter ($\mu\text{g/kg/min} \leftrightarrow \text{mL/hr}$), active patient weight synchronization, and auto-generated 6-step titration reference ladders.

#### 5. 👶 Pediatric & Neonatal Growth Suite
* **WHO Child Growth Standards (0–5 Years):** Piecewise LMS curves calculating exact Z-scores (SD) and percentiles for Weight-for-Age, Height-for-Age, and BMI-for-Age.
* **Age-Stratified Vital Sign Norms:** Reference ranges (50th & 95th percentiles) across 7 developmental stages (Preterm, Term Neonate, Infant, Toddler, Preschool, School Age, Adolescent).
* **Neonatal Scoring:** Interactive 1- and 5-minute APGAR scores and Silverman-Anderson Respiratory Distress Index for newborns.

#### 6. 💊 100% Offline Drug Interaction Safety Matrix *(Epocrates Benchmark)*
* 50+ critical drug-pair and pharmacodynamic class rules running client-side in $<2\text{ ms}$ with zero network reliance.
* Catches fatal combinations (MAOI + Pethidine/Tramadol, PDE5-i + Nitrates, Epinephrine 1:1,000 IV), anesthesia risks (Opioid + Benzo respiratory depression, Suxamethonium + Hyperkalemia), and acute care risks (Triple Whammy AKI: ACEi + Diuretic + NSAID; Linezolid Serotonin Syndrome; QT prolongation).

#### 7. 📝 Structured Clinical Documentation (SOAP & SBAR)
* 1-tap compiler generating complete **Subjective, Objective, Assessment, Plan (SOAP)** clinical progress notes and **Bedside Rounds SBAR** handoff sheets with embedded risk scores, active labs, and emergency standby parameters for direct copy-pasting into hospital EHR/HIS.

#### 8. 🚨 Acute Crisis Resuscitation Tray
* Interactive ACLS/PALS 2-minute CPR cycle metronome ($110\text{ bpm}$ via Web Audio API) with audio alarms and shockable vs non-shockable branches.
* Pediatric Broselow tape emergency estimator (9 color zones + endotracheal tube / airway sizing).
* Multi-route Anaphylaxis protocols (Thigh IM vs Monitored OR/ICU Push-Dose vs Continuous Infusion).
