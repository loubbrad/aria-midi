# The Aria-MIDI Dataset

[Paper](https://openreview.net/pdf?id=X5hrhgndxW) / [HuggingFace](https://huggingface.co/datasets/loubb/aria-midi)

The Aria-MIDI dataset is a collection of 1,186,253 MIDI files, comprising approximately 100,629 hours of transcribed solo-piano recordings, with metadata in categories including genre, composer, performer, as well as compositional identifiers. We developed Aria-MIDI to serve as a dataset for pre-training generative music models, and are releasing it with the goal of facilitating open research in music information retrieval and generative modeling for symbolic music.

❗ **NOTE:** NOTE: For applications to generative modeling, it's highly recommended to use the **pruned** subset which has been filtered and post-processed accordingly, which was used to pre-train [Aria](https://github.com/EleutherAI/aria).

## Download (V1)

Along with the full dataset, we provide several subsets which may be appropriate for different use cases: 

| Subset        | # Files   | Deduplication[^1] | Pre-processing filters[^2]        | Example Application                    |
|---------------|-----------|--------------------|------------------------------------|----------------------------------------|
| Full [[download](https://huggingface.co/datasets/loubb/aria-midi/resolve/main/aria-midi-v1-ext.tar.gz?download=true)]       | 1,186,253 | No                 | None                               | Data analysis                          |
| **Pruned** [[download](https://huggingface.co/datasets/loubb/aria-midi/resolve/main/aria-midi-v1-pruned-ext.tar.gz?download=true)]     | 820,944   | 10                 | Light                              | Foundation model pre-training          |
| Deduped [[download](https://huggingface.co/datasets/loubb/aria-midi/resolve/main/aria-midi-v1-deduped-ext.tar.gz?download=true)]    | 371,053   | 1                  | Heavy                              | Generative modeling                   |
| Unique [[download](https://huggingface.co/datasets/loubb/aria-midi/resolve/main/aria-midi-v1-unique-ext.tar.gz?download=true)]     | 32,522    | 1                  | Compositional metadata[^3]         | Composition fingerprints               |

[^1]: For popular composers, we retain at most *X* instances for each opus/piece-number pair and discard files lacking compositional identifiers.  
[^2]: Heuristic-based filtering, considering note density, pitch and duration entropy, silence, number of segments, and indicators of repetitive content, to exclude problematic entries.  
[^3]: Exclude all files lacking exact compositional identifiers.

The specific pre-processing filters we employed can be reproduced using utilities in the [aria-utils](https://github.com/EleutherAI/aria-utils) library. We provide the exact settings as JSON files: [light](https://huggingface.co/datasets/loubb/aria-midi/blob/main/light-preprocess.json) and [heavy](https://huggingface.co/datasets/loubb/aria-midi/blob/main/heavy-preprocess.json).

## Dataset

As detailed in our [paper](https://openreview.net/pdf?id=X5hrhgndxW), Aria-MIDI was created by transcribing publicly available solo-piano audio recordings into MIDI files. To accomplish this, we developed various tools which we integrated into our data pipeline, which we also release under the [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0) license. In particular: 

[Aria-AMT](https://github.com/EleutherAI/aria-amt). A seq-to-seq piano transcription model which accurately transcribes notes from solo-piano recordings to MIDI. We designed this model to be robust, aiming to maintain note-identification accuracy when transcribing realistic audio from a wide range of recording environments. We also provide an inference engine for Linux/CUDA, supporting batched processing as well as multi-gpu setups.

[Aria-CL](https://github.com/loubbrad/aria-cl). A solo-piano audio-classification model capable of detecting and isolating (i.e., segmenting) solo-piano content from arbitrary audio files. We designed our approach to additionally exclude solo-piano audio which may cause problems for transcription models, e.g., recordings with significant audio artifacts.

We provide the dataset as a tarball. Filenames follow the format `<file_id>_<segment_number>.mid`, where each segment number corresponds to a distinct (i.e., non-overlapping), contiguous portion of solo-piano content from each audio file, identified and transcribed independently. Metadata is provided in the file metadata.json, structured as: 

```
<file_id>: {
  "metadata": {
    <metadata_category>: <metadata>,
    ...,
  },
  "audio_scores": {
    <segment_number>: <score>,
    ...,
  }
}
```

Metadata was extracted from textual information associated with each original audio file and may vary in accuracy. Audio scores are calculated as the average score assigned by our classifier across segments, designed to correlate with the audio quality of the underlying solo-piano recording.

## Citation/License

Aria-MIDI is distributed with the [CC-BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.en) license. By accessing this dataset, you declare that you agree to our [disclaimer](https://github.com/loubbrad/aria-midi/blob/main/DISCLAIMER.md). If you use the dataset, or any of the components in our data-pipeline, please cite the paper in which they were introduced:

```
@inproceedings{bradshawaria,
  title={Aria-MIDI: A Dataset of Piano MIDI Files for Symbolic Music Modeling},
  author={Bradshaw, Louis and Colton, Simon},
  booktitle={International Conference on Learning Representations},
  year={2025},
  url={https://openreview.net/forum?id=X5hrhgndxW}, 
}
```

