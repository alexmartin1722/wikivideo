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

##### Videos from MultiVENT2.0 (WikiVideo 2024)
These videos range from 2015-2024.
```bash
# untar the videos 
tar -xvzf videos.tar.gz -C data/videos
# untar the audios
tar -xvzf audios.tar.gz -C data/audios
```

##### Videos from the year 2025 (WikiVideo25)
These videos are for the year 2025. They are also the videos used in the MAGMaR shared task at ACL 2026. 
```bash
# untar the videos 
tar -xvzf videos_2025.tar.gz -C data/videos
# untar the audios
tar -xvzf audios_2025.tar.gz -C data/audios
```

#### Finish
Now you should be done. You will see a `annotations` folder in the huggingface repo, but this also exists in the `data/` folder already in the `data/wikivideo` directory. 


### Dataset Format
In the `data/wikivideo` directory, you will find the file `final_data.json` which has the articles. This file is formatted as a dictonary:
```json
{
    "Wikipedia Title": {
        "claims": [["claim1", "claim2", ...], ...], 
        "original_article": ["sent1", "sent2", ...],
        "claims_to_supporting_videos": {
            "claim1": {
                "supporting_videos": ["video_id1", "video_id2", ...],
                "videos_modalities": {
                    "video_id1": {
                        "video": true,
                        "audio": false,
                        "ocr": true
                    },
                    ...
                }
            }
        },
        "article": "The article text",
        "query_id": "query id",
        "videos": {
            "video_id1": {
                "anon_scale_id": "XXX",
                "language": "english",
                "video_type": "Professional | Edited | Diet Raw | Raw",
                "relevance": 3
        }
    },
    ...
}
```
In this json, you see that the top level key is the Wikipeda Article Title. Each other key is defined as follows:
- `claims`: The claims from the original article
- `original_article`: The original article split into sentences
- `claims_to_supporting_videos`: A mapping from each claim to the videos that support it along with the modalities present in each video
- `article`: This is the human written article on the topic using the video data.
- `query_id`: This is the query id for the article from the MultiVENT 2.0 dataset. This will be helpful when doing RAG experiments.
- `videos`: Metadata about each video used in the article


