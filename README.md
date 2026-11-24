# ML for Cybersecurity

This project compares a majority baseline, scikit-learn logistic regression, and a PyTorch linear classifier on the NSL-KDD network intrusion detection benchmark.

- [Executed notebook](train.ipynb)
- [Assignment](docs/ML_CyberSecurity_Assignment.md)
- [Learning guide](docs/LEARNING_GUIDE.md)
- [Report](docs/REPORT.md)

## Reproduce the notebook

From the repository root, download both text files to `data/` and keep the provided split. This mirror provides comma-separated copies with the original labels and difficulty field. [NSL-KDD file mirror](https://github.com/defcom17/NSL_KDD)

```sh
mkdir -p data
curl -fL 'https://raw.githubusercontent.com/defcom17/NSL_KDD/master/KDDTrain%2B.txt' -o 'data/KDDTrain+.txt'
curl -fL 'https://raw.githubusercontent.com/defcom17/NSL_KDD/master/KDDTest%2B.txt' -o 'data/KDDTest+.txt'
uv sync --locked --group torch
uv run --locked --group torch jupyter execute --inplace train.ipynb
```

You can also open the notebook in an editor, select the project’s `.venv` as the kernel, and run all cells. Omit `--group torch` from the uv commands for the scikit-learn-only run.

## Dataset attribution

ISCX NSL-KDD, Canadian Institute for Cybersecurity, University of New Brunswick. [Dataset description](https://www.unb.ca/cic/datasets/nsl.html)

M. Tavallaee, E. Bagheri, W. Lu, and A. A. Ghorbani, “A Detailed Analysis of the KDD CUP 99 Data Set,” IEEE CISDA, 2009. [Paper](https://doi.org/10.1109/CISDA.2009.5356528)
