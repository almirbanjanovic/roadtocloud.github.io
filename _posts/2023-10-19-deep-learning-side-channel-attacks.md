---
title:  "Deep-Learning Side-Channel Attacks"
excerpt: "Deep learning side channel attacks involve exploiting unintended information leakage from the implementation of deep learning models to infer sensitive information or manipulate their behavior."
toc: true
tags:
  - security
  - ai
  - ml
---

Deep learning side channel attacks involve exploiting unintended information leakage from the implementation of deep learning models to infer sensitive information or manipulate their behavior. Recently I visited a presentation by Dr. Boyang Wang from the University of Cincinnati.  Below I'll give an overview of what deep-learning side-channel attacks are and offer my own critique.

# Synopsis
In general terms, “side channel attacks” are attacks that use “side channel information”, which is information that can be retrieved from an encryption device. (Medium-2) In this project, an attacker uses a side-channel attack (SCA) to analyze power draw of a specific target when it runs encryption with the aim of recovering its encryption key. An attacker is obviously a malicious actor; a power draw can be analyzed using power traces; AES encryption can be used. The immediate question of “Why” arises. Why is this possible? This is possible because power consumption is correlated with the intermediate values of encryption. In simpler terms, when an encryption algorithm runs, more power is drawn. In even simpler terms, computing one byte with all zeros consumes less power than one byte with all ones.
Furthermore, this SCA uses deep learning because of some inherent advantages:

1. Requires no (or less) pre-processing over power traces while measuring power consumption,
2. Can defeat countermeasures such as masking and random delays,
3. If model is well-trained, it can recover keys with a small number of traces.
   
In general, SCAs can be categorized into two separate types: profiling attacks, which use a training device and a testing device, and non-profiling attacks, which only use the test device.

<figure style="width: 580px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/profiling-attacks.png" alt="">
  <figcaption>Profiling attacks.</figcaption>
</figure> 
