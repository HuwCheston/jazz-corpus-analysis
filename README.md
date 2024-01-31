# 🎹🎻🥁 Cambridge Jazz Trio Database 🎹🎻🥁

The **Cambridge Jazz Trio Database** is a dataset composed of about 16 hours of jazz performances annotated by an automated signal processing pipeline.

## Contents:
- [Dataset](#dataset)
- [Download](#download)
- [License](#license)
- [Citation](#outputs)

## Dataset

![Pipeline Overview](https://raw.githubusercontent.com/HuwCheston/Cambridge-Jazz-Trio-Database/main/references/images/pipeline_overview.jpg)

The database contains about 16 hours of audio recorded between 1947–2015 with associated timing annotations. The annotations were generated by an automated signal processing pipeline: two source separation models [[1](https://github.com/deezer/spleeter), [2](https://github.com/facebookresearch/demucs)] are applied to obtain isolated stems from every instrument in each recording, onsets are tracked in these stems using [[3](https://github.com/librosa/librosa)], and these onsets are matched with the nearest quarter note pulse tracked in the audio mixture using [[4](https://github.com/CPJKU/madmom)].

All recordings consist of [jazz piano trios](https://en.wikipedia.org/wiki/Jazz_trio) ensembles and feature one of 10 different jazz pianists. In roughly half of recordings, the pianist was one of the top-10 most prolific and popular musicians in the piano trio format, identified through large-scale scraping of [MusicBrainz](https://musicbrainz.org/doc/MusicBrainz_API) and [Last.FM](https://www.last.fm/api) data. In all other recordings, the pianist is [Bill Evans](https://en.wikipedia.org/wiki/Bill_Evans), widely acknowledged as one of the most influential jazz pianists of all time. This split enables models to be trained on either multi-class (i.e., *which pianist is it?*, using only the top-10 pianist recordings) and binary (i.e., *is the pianist Bill Evans?*, using all the data) classification problems.

Over 10% of the dataset also has corresponding ground truth annotations, created manually by the research team. These annotations were used to set the parameters of our detection algorithms, to maximize the correspondence between human- and algorithm-detected onsets (see `references\parameter_optimisation\`). Across these reference sets, the average *F*-score was .86. Onsets detected by human annotators and algorithms are aligned with ~5 ms accuracy, on average. 

Audio recordings are not stored directly in this repository. Instead, when you run the script to build the dataset, these files will be downloaded automatically from an official YouTube source (see the [Download](#download) section, below). Note that, occasionally, individual YouTube recordings may be taken down: these tracks will be skipped when building the dataset, but please report any issues [here](mailto:huwcheston@gmail.com?subject=CJD Missing YouTube link) so that working links can be added in these cases.

Below is an example of what the data looks like:

![Data Example](https://raw.githubusercontent.com/HuwCheston/Cambridge-Jazz-Trio-Database/main/references/images/data_example.jpg)

## Download

The Cambridge Jazz Trio Database is provided as a `.zip` file containing the onset and beat annotations in `.csv` format, with metadata in `.json` format. The source code for building these onsets is also available publicly. 

To download the database, navigate to [`Releases`](https://github.com/HuwCheston/Cambridge-Jazz-Trio-Database/releases) and download the most recent `.zip` file.

The metadata files contain the following fields for every recording:

| Field               | Type | Description                                                                                   |
|---------------------|------|-----------------------------------------------------------------------------------------------|
| `track_name`        | str  | Title of the recording                                                                        |
| `album_name`        | str  | Title of the earliest album released that contains the track                                  |
| `recording_year`    | int  | Year of recording                                                                             |
| `channel_overrides` | dict | Key-value pairs relating to panning: `piano: l` means the piano is panned to the left channel |
| `mbz_id`            | str  | Unique ID assigned to the track on MusicBrainz                                                |
| `time_signature`    | int  | The number of quarter note beats per measure for the track                                    |
| `first_downbeat`    | int  | The first clear quarter note downbeat in the track                                            |
| `rating_audio`      | int  | Subjective rating (1–3, 3 = best) of source-separation quality                                |
| `rating_detection`  | int  | Subjective rating of onset detection quality                                                  |
| `links`             | dict | YouTube URL for the recording                                                                 |
| `excerpt_duration`  | str  | Duration of recording, in `Minutes:Seconds` format                                            |
| `timestamps`        | dict | Start and end timestamps for the piano solo in the recording                                  |
| `musicians`         | dict | Key-value pairs of the musicians included in the recording                                    |
| `fname`             | str  | Audio filename                                                                                |

## Process your own tracks

To process a piano trio recording using our pipeline, you can use a command line interface to run the code in `src/process.py`. For example, to process 30 seconds of audio from [Chick Corea's Akoustic Band 'Spain'](https://www.youtube.com/watch?v=BguWLXMARHk):

```
git clone https://github.com/HuwCheston/Cambridge-Jazz-Trio-Database.git
cd Cambridge-Jazz-Trio-Database
python -m venv venv
call venv/Scripts/activate.bat    # Windows
source venv/bin/activate    # Ubuntu/OSX
pip install -r requirements.txt
python src/process.py -i "https://www.youtube.com/watch?v=BguWLXMARHk" --begin "03:00" --end "03:30"
```

This will create a new folder in the root directory of the repository: source audio is stored in `/data`, annotations in `/annotations`, and extracted features in `/outputs`. Extracted features follow the format given in `Cheston, Schlichting, Cross, & Harrison (2024b)`.

By default, the script will use the parameter settings described in `Cheston, Schlichting, Cross, & Harrison (2024a)` for extracting onsets and beats. This can be changed by passing `-p`/`--params`, followed by the name of a folder (inside `references/parameter_optimisation`) containing a `converged_parameters.json` file.

The script will also use a set of default parameters for the given track (e.g. time signature). To override these, pass in the `-j`/`--json` argument, followed by a path to a `.json` file following the format outlined in the `metadata` table above.


## License

The dataset is made available under the [MIT License](https://spdx.org/licenses/MIT.html). Please note that your use of the audio files linked to on YouTube is not covered by the terms of this license.

## Citation

If you use the Cambridge Jazz Trio Database in your work, please cite the paper where it was introduced:

```
@misc{
	title = {Cambridge Jazz Trio Database: Automated Timing Annotation of Jazz Piano Trio Recordings Processed Using Audio Source Separation},
	url = {osf.io/preprints/psyarxiv/jyqp3},
	doi = {10.31234/osf.io/jyqp3},
	publisher = {PsyArXiv},
	author = {Cheston, Huw and Schlichting, Joshua L and Cross, Ian and Harrison, Peter M C},
	month = jan,
	year = {2024},
}
```

## Outputs

Creation of the database has resulted in the following published research outputs:

- Cheston, H., Schlichting, J. S., Cross, I., & Harrison, P. M. C. (2024a). Cambridge Jazz Trio Database: Automated Timing Annotation of Jazz Piano Trio Recordings Processed Using Audio Source Separation [Preprint]. PsyArXiv. [https://doi.org/10.31234/osf.io/jyqp3](https://doi.org/10.31234/osf.io/jyqp3).
- Cheston, H., Schlichting, J. S., Cross, I., & Harrison, P. M. C. (2024b). Rhythmic Qualities of Jazz Improvisation Predict Performer Identity and Style in Source-Separated Audio Recordings [Preprint]. PsyArXiv. [https://doi.org/10.31234/osf.io/txy2f](https://doi.org/10.31234/osf.io/txy2f).
- Cheston, H., Cross, I., & Harrison, P. M. C. (2023). An Automated Pipeline for Characterizing Timing in Jazz Trios. Proceedings of the DMRN+18 Digital Music Research Network. Digital Music Research Network, Queen Mary University of London, London, United Kingdom.
