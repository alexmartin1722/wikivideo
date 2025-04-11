# WikiVideo: Article Generation from Multiple Videos

Paper: [arxiv](https://arxiv.org/abs/2504.00939)

## Code
coming soon... (a lot of cleanup and demo coming)


## Data 
HuggingFace link: [WikiVideo Dataset](https://huggingface.co/datasets/hltcoe/wikivideo)


### Download instructions
The dataset can be found on [huggingface](https://huggingface.co/datasets/hltcoe/wikivideo). However, you can't use the datasets library to access the videos because everything is tarred. Instead you need to locally download the dataset and then untar the videos (and audios if you use those).

#### Step 1: Install git-lfs
The first thing you need to do is make sure that git-lfs is installed, otherwise you won't be able to pull the video and audio tar files.
```bash 
git lfs install
```

#### Step 2: Clone the dataset
After enabling git-lfs, you can now pull the dataset from huggingface. 
```bash
git clone https://huggingface.co/datasets/hltcoe/wikivideo
```
I would also tmux this because it might take a while. 


#### Step 3: Untar the videos
In the `data/` folder, you will see places for `data/audios` and `data/videos`. You need to untar the videos and audios into these folders. The audios file is `audios.tar.gz` and the videos is `videos.tar.gz`. 
```bash
# untar the videos 
tar -xvzf videos.tar.gz -C data/videos
# untar the audios
tar -xvzf audios.tar.gz -C data/audios
```

#### Finish
Now you should be done. You will see a `annotations` folder in the huggingface repo, but this also exists in the `data/` folder already in the `data/wikivideo` directory. 


### Dataset Format
In the `data/wikivideo` directory, you will find the file `final_data.json` which has the articles. This file is formatted as a dictonary:
```json
{
    "Wikipedia Title": {
        "article": "The article text",
        "query_id": "test_pt2_query_XXX",
        "original_article": ["sent1", "sent2", ...],
        "audio_lists": [[true, false, true, ...], ...],
        "video_lists": [[true, false, true, ...], ...],
        "ocr_lists": [[true, false, true, ...], ...],
        "neither_lists": [[true, false, true, ...], ...],
        "video_ocr_lists": [[true, false, true, ...], ...],
        "claims": [["claim1", "claim2", ...], ...],
        "videos":{
            "video_id": {
                "anon_scale_id": "XXX",
                "language": "english",
                "video_type": "Professional | Edited | Diet Raw | Raw",
                "relevance": 3,
            }
        }
    },
    ...
}
```
In this json, you see that the top level key is the Wikipeda Article Title. Each other key is defined as follows:
- `article`: This is the human written article on the topic using the video data. 
- `query_id`: This is the query id for the article from the MultiVENT 2.0 dataset. This will be helpful when doing RAG experiments.
- `original_article`: This is the original Wikipedia article from MegaWika 2.0. It is sentence tokenized. Each sentence corresponsed to an index in the audio_lists, video_lists, ocr_lists, neither_lists, video_ocr_lists, and claims lists.
- `claims`: This is a list of claims that are in the article. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a human written claim from the sentence. These specific claims correspond to the boolean elements in the audio_lists, video_lists, ocr_lists, neither_lists, and video_ocr_lists.
- `audio_lists`: This is a list of lists of booleans. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a claim. If the boolean is TRUE then the claim is supported by the audio, otherwise it is not.
- `video_lists`: This is a list of lists of booleans. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a claim. If the boolean is TRUE then the claim is supported by the video, otherwise it is not.
- `ocr_lists`: This is a list of lists of booleans. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a claim. If the boolean is TRUE then the claim is supported by the ocr, otherwise it is not.
- `neither_lists`: This is a list of lists of booleans. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a claim. If the boolean is TRUE then the claim is supported by any of the modalities. 
- `video_ocr_lists`: This is a list of lists of booleans. Each index corresponds to a sentence in the original article. Then each index in the sublist corresponds to a claim. If the boolean is TRUE then the claim is supported by the video or ocr, otherwise it is not.
- `videos`: This is a dictionary of videos relevant to the query topic. The key is the video id and the value is a dictionary of video metadata. The metadata is defined as follows:
    - `anon_scale_id`: This is the anonnoynmous ID used in MultiVENT 2.0 for the video. This will help you deduplicate the test set of the dataset when doing video retrieval in the RAG experiments.  
    - `language`: This is the language of the video. 
    - `video_type`: This is the type of video. 
    - `relevance`: This is the relevance of the video to the article. These values range from 0-3.