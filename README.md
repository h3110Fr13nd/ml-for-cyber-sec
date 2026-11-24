# ML for Cybersecurity

This project compares a majority baseline, scikit-learn logistic regression, and a PyTorch linear classifier on the NSL-KDD network intrusion detection benchmark.

- [Executed notebook](deliverables/train.ipynb)
- [Assignment](docs/ML_CyberSecurity_Assignment.md)
- [Learning guide](docs/LEARNING_GUIDE.md)
- [Report](deliverables/REPORT.docx)

## Reproduce the notebook

Open `deliverables/train.ipynb` in a Python 3.10+ Jupyter kernel and run all cells. The notebook installs missing core packages, downloads and verifies both NSL-KDD files, and writes the plots beside the notebook when launched from its folder. The first run needs internet access and a writable working directory. PyTorch is optional.

To run both models with the repository's locked environment, run this from the repository root.

```sh
uv sync --locked --group torch
uv run --locked --group torch jupyter execute --inplace deliverables/train.ipynb
```

## Dataset attribution

ISCX NSL-KDD, Canadian Institute for Cybersecurity, University of New Brunswick. [Dataset description](https://www.unb.ca/cic/datasets/nsl.html)

M. Tavallaee, E. Bagheri, W. Lu, and A. A. Ghorbani, “A Detailed Analysis of the KDD CUP 99 Data Set,” IEEE CISDA, 2009. [Paper](https://doi.org/10.1109/CISDA.2009.5356528)
