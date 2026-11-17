# Data availability

We used the fixed Yelp2018 and Amazon-book processed interaction splits published with LightGCN. [LightGCN](https://doi.org/10.1145/3397271.3401063)

| Dataset | Users | Items | Train interactions | Test interactions | Empty test users retained |
| --- | ---: | ---: | ---: | ---: | ---: |
| Yelp2018 | 31,668 | 38,048 | 1,237,259 | 324,147 | 0 |
| Amazon-book | 52,643 | 91,599 | 2,380,730 | 603,378 | 4 |

Download `train.txt` and `test.txt` for each dataset. Place them under `data/lightgcn_text/yelp2018/` and `data/lightgcn_text/amazon-book/`. [Yelp2018 splits](https://github.com/kuandeng/LightGCN/tree/master/Data/yelp2018) · [Amazon-book splits](https://github.com/kuandeng/LightGCN/tree/master/Data/amazon-book)

We verify the input file sizes and SHA-256 hashes against [configs/raw_inputs.json](configs/raw_inputs.json). We record matrix shapes, interaction counts, and hashes of the prepared files and their canonical sparse contents in [configs/inputs.json](configs/inputs.json). The checks identify the inputs used in this study. The exact upstream commit originally downloaded is unverified. [Preparation commands](README.md#prepare-the-data)

We distribute preparation scripts, checksums, and aggregated results. Obtain the interaction splits from the upstream repositories. The study uses the processed splits and leaves the reconstruction of the raw-to-processed pipeline outside its scope. [Yelp data terms](https://s3-media3.fl.yelpcdn.com/assets/srv0/engineering_pages/bea5c1e92bf3/assets/vendor/yelp-dataset-agreement.pdf) · [Original Amazon review corpus](https://cseweb.ucsd.edu/~jmcauley/datasets/amazon/links.html)
