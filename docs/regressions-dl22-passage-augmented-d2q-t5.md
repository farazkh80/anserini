# Anserini Regressions: TREC 2022 Deep Learning Track (Passage)

**Models**: BM25 with doc2query-T5 expansions on augmented passages

This page describes document expansion experiments (with doc2query-T5), integrated into Anserini's regression testing framework, on the TREC 2022 Deep Learning Track passage ranking task using the MS MARCO V2 _augmented_ passage collection.

Note that the NIST relevance judgments provide far more relevant passages per topic, unlike the "sparse" judgments provided by Microsoft (these are sometimes called "dense" judgments to emphasize this contrast).
For additional instructions on working with MS MARCO V2 passage collection, refer to [this page](experiments-msmarco-v2.md).

The exact configurations for these regressions are stored in [this YAML file](../src/main/resources/regression/dl22-passage-augmented-d2q-t5.yaml).
Note that this page is automatically generated from [this template](../src/main/resources/docgen/templates/dl22-passage-augmented-d2q-t5.template) as part of Anserini's regression pipeline, so do not modify this page directly; modify the template instead.

From one of our Waterloo servers (e.g., `orca`), the following command will perform the complete regression, end to end:

```
python src/main/python/run_regression.py --index --verify --search --regression dl22-passage-augmented-d2q-t5
```

## Indexing

Typical indexing command:

```
target/appassembler/bin/IndexCollection \
  -collection MsMarcoV2PassageCollection \
  -input /path/to/msmarco-v2-passage-augmented-d2q-t5 \
  -index indexes/lucene-index.msmarco-v2-passage-augmented-d2q-t5/ \
  -generator DefaultLuceneDocumentGenerator \
  -threads 24 -storeRaw \
  >& logs/log.msmarco-v2-passage-augmented-d2q-t5 &
```

The value of `-input` should be a directory containing the compressed `jsonl` files that comprise the corpus.
See [this page](experiments-msmarco-v2.md) for additional details.

For additional details, see explanation of [common indexing options](common-indexing-options.md).

## Retrieval

Topics and qrels are stored in [`src/main/resources/topics-and-qrels/`](../src/main/resources/topics-and-qrels/).
The regression experiments here evaluate on the 76 topics for which NIST has provided judgments as part of the TREC 2022 Deep Learning Track.

<!-- update link once data becomes public
The original data can be found [here](https://trec.nist.gov/data/deep2022.html).
-->

After indexing has completed, you should be able to perform retrieval as follows:

```
target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-v2-passage-augmented-d2q-t5/ \
  -topics src/main/resources/topics-and-qrels/topics.dl22.txt \
  -topicreader TsvInt \
  -output runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt \
  -bm25 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-v2-passage-augmented-d2q-t5/ \
  -topics src/main/resources/topics-and-qrels/topics.dl22.txt \
  -topicreader TsvInt \
  -output runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt \
  -bm25 -rm3 -collection MsMarcoV2PassageCollection &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-v2-passage-augmented-d2q-t5/ \
  -topics src/main/resources/topics-and-qrels/topics.dl22.txt \
  -topicreader TsvInt \
  -output runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt \
  -bm25 -rocchio -collection MsMarcoV2PassageCollection &
```

Evaluation can be performed using `trec_eval`:

```
tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m recip_rank -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default.topics.dl22.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m recip_rank -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rm3.topics.dl22.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 100 -m recip_rank -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.bm25-default+rocchio.topics.dl22.txt
```

Note that the TREC 2022 passage qrels are not publicly available (yet).
However, if you are a participant, you can download them from the NIST "active participants" site.
Place the qrels file in `src/main/resources/topics-and-qrels/qrels.dl22-passage.txt` for the above evaluation commands to work.

## Effectiveness

With the above commands, you should be able to reproduce the following results:

| **MAP@100**                                                                                                  | **BM25 (default)**| **+RM3**  | **+Rocchio**|
|:-------------------------------------------------------------------------------------------------------------|-----------|-----------|-----------|
| [DL22 (Passage)](https://microsoft.github.io/msmarco/TREC-Deep-Learning)                                     | 0.0870    | 0.0949    | 0.0989    |
| **MRR@100**                                                                                                  | **BM25 (default)**| **+RM3**  | **+Rocchio**|
| [DL22 (Passage)](https://microsoft.github.io/msmarco/TREC-Deep-Learning)                                     | 0.4396    | 0.4642    | 0.4581    |
| **nDCG@10**                                                                                                  | **BM25 (default)**| **+RM3**  | **+Rocchio**|
| [DL22 (Passage)](https://microsoft.github.io/msmarco/TREC-Deep-Learning)                                     | 0.3607    | 0.3734    | 0.3794    |
| **R@100**                                                                                                    | **BM25 (default)**| **+RM3**  | **+Rocchio**|
| [DL22 (Passage)](https://microsoft.github.io/msmarco/TREC-Deep-Learning)                                     | 0.2586    | 0.2496    | 0.2558    |
| **R@1000**                                                                                                   | **BM25 (default)**| **+RM3**  | **+Rocchio**|
| [DL22 (Passage)](https://microsoft.github.io/msmarco/TREC-Deep-Learning)                                     | 0.5054    | 0.5192    | 0.5268    |

**IMPORTANT**: These runs are evaluated prior to dedup, so the scores will be slightly lower than the official scores (e.g., computed by NIST), which includes dedup.

The "BM25 (default)" condition corresponds to the `paug_d2q_bm25` run submitted to the TREC 2022 Deep Learning Track as a "baseline".
As of [`91ec67`](https://github.com/castorini/anserini/commit/91ec6749bfef206e210bcc1df8cd4060e7d7aaff), this correspondence was _exact_.
That is, modulo the runtag and the number of hits, the output runfile should be identical.
This can be confirmed as follows:

```bash
# Trim out the runtag:
cut -d ' ' -f 1-5 runs/paug_d2q_bm25 > runs/paug_d2q_bm25.submitted.cut

# Trim out the runtag and retain only top 100 hits per query:
cut -d ' ' -f 1-5 runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default | grep -E '^[^ ]+ Q0 [^ ]+ (\d|\d\d|100) ' > runs/paug_d2q_bm25.new.cut

# Verify the two runfiles are identical:
diff runs/paug_d2q_bm25.submitted.cut runs/paug_d2q_bm25.new.cut
```

The "BM25 + RM3" and "BM25 + Rocchio" conditions above correspond to the `paug_d2q_bm25rm3` run and the `paug_d2q_bm25rocchio` run submitted to the TREC 2022 Deep Learning Track as "baselines".
However, due to [`a60e84`](https://github.com/castorini/anserini/commit/a60e842e9b47eca0ad5266659081fe1180c96b7f), the results are slightly different (because the underlying implementation changed).

To reproduce the official NIST scores, we need to run dedup.
Official submissions start off with only 100 hits per query, which is (slightly) reduced after the dedup process.
Thus, we have to take our runs (which contain 1000 hits per query) and trim down to 100 hits per query prior to running dedup.
These commands are shown below:

```bash
# Trim from 1000 hits to 100 hits
python tools/scripts/trim_run_to_top_k.pl --k 100 --input runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default --output runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default.hits100
python tools/scripts/trim_run_to_top_k.pl --k 100 --input runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3 --output runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3.hits100
python tools/scripts/trim_run_to_top_k.pl --k 100 --input runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio --output runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio.hits100

# Run dedup
python tools/scripts/dedup.py tools/topics-and-qrels/msmarco-v2-passage-neardupes.txt.gz \
  runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default.hits100 \
  runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3.hits100 \
  runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio.hits100

# Evaluate
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m ndcg_cut.10 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio.hits100.dedup

tools/eval/trec_eval.9.0.4/trec_eval -c -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m map -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio.hits100.dedup

tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rm3.hits100.dedup
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 -l 2 src/main/resources/topics-and-qrels/qrels.dl22-passage.txt runs/run.msmarco-v2-passage-augmented-d2q-t5.dl22.bm25-default+rocchio.hits100.dedup
```

With the above commands, we should arrive at the following sores:

|              | **BM25 (default)** | **+RM3**  | **+Rocchio**|
|:-------------|--------------------|-----------|-------------|
| **nDCG@10**  | 0.3508             | 0.3562    | 0.3656      |
| **MAP@100**  | 0.0807             | 0.0867    | 0.0909      |
| **R@100**    | 0.2434             | 0.2346    | 0.2410      |
