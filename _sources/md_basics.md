---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.10.3
kernelspec:
  display_name: ase [~/.conda/envs/ase/]
  language: python
  name: python3
---

+++ {"tags": []}

Molecular dynamics basics
=========================

Static DFT or other ab-initio (Quantum chemistry) calculations provide us a lot of useful and very accurate information. However, atoms and molecules around us are in constant motion at finite temperatures.
 Molecular dynamics(MD) simulation techniques model this motion. The driving force behind the motion could be ab-intio calculations or empirical potentials. Several
 flavors of MD have been developed among ab-initio and classical techniques to tackle different type of processes. Empirical potentials can be further classified into reactive and 
non-reactive potentials. Recently, an additional flavor of machine learning (ML) based potentials is also being developed.  Different molecular dynamics techniques have different 
levels of accuracy and uses. 

> There are no wrong MD techniques, just wrong applications. - (Prof. Adri van Duin)
