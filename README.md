# Synthetic PNR Dataset (v1.86)

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC--BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)
![Dataset Size](https://img.shields.io/badge/Data-4.7M%20Passengers-blue)
![Version](https://img.shields.io/badge/Version-v1.86-orange)


This repository contains a synthetic Passenger Name Record (PNR) dataset simulating international flight bookings across 36 countries in the Schengen region during 2019.

The dataset was generated for research into airline passenger modeling, mobility analysis, and aviation security — with full privacy preservation. It supports tasks such as travel pattern analysis, network flow modeling, risk detection research, and synthetic data benchmarking.


## 📦 Contents

- `data/sample/`: Example XML PNR files (full dataset available in release)
- `docs/technical_report.md`: Full data-release report with methodology, validation, and limitations
- `scripts/`: Generation pipeline (population, social networks, bookings)

## 🧪 Dataset Features

- Over 4.7 million synthetic passengers
- More than 4,000 flights simulated over 2019 window
- XML format conforming to IATA PNRGOV schema
- Configurable by nationality, trip type, and group size
- Built from public sources (UN, Eurostat, World Bank)

## 📄 Citation

If you use this dataset, please cite the accompanying paper:

> > Fadlian, M. F., Ireson, N., & Lanfranchi, V. (2025). *Generating realistic Passenger Name Records with privacy compliance for security analysis*. Proceedings of the International ISCRAM Conference. [https://doi.org/10.59297/mbf3dq94](https://doi.org/10.59297/mbf3dq94)


A DOI for the dataset will be issued in the next release via Zenodo.

## 📜 License

This dataset is released under the Creative Commons Attribution 4.0 International (CC-BY-NC 4.0) License.

## 🔗 Links

- [Technical Report](docs/technical_report.md)
- [Validation Notebook](notebooks/validation.ipynb)

