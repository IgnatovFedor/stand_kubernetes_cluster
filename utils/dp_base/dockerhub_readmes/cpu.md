Pre-built DeepPavlov models served via HTTP (using CPU).



This repository contains pre-built DeepPavlov images for different library releases (`latest` tag equals to the latest
release in this repo). These images allow you to run DeepPavlov models and communicate them via REST-like HTTP API
(see [riseapi](http://docs.deeppavlov.ai/en/master/integrations/rest_api.html) DeepPavlov docs for more details).

Images from these repository are built to be run on CPU. To use GPU please explore
[base-gpu](https://hub.docker.com/r/deeppavlov/base-gpu) repo.

Dockerfile can be found [here](https://github.com/deepmipt/stand_kubernetes_cluster/blob/master/utils/dp_base/cpu/Dockerfile).

### Instructions

Run following to rise server for particular DeepPavlov model in CPU mode:
```
docker run -e CONFIG=<dp_config_name> -p <your_port>:5000 \
    -v <dp_components_volume>:/root/.deeppavlov \
    -v <host_venv_dir>:/venv \
    deeppavlov/base-cpu:<dp_version>
```

Where:

1. `<dp_config_name>` (mandatory) - is config file name (**without extension**) for model you want to serve. You can get DeepPavlov
`latest` models list with description in [DP features docs](http://docs.deeppavlov.ai/en/master/features/overview.html)
or browse DP gitHub [here](https://github.com/deepmipt/DeepPavlov/tree/master/deeppavlov/configs).

2. `<your_port>` (mandatory) - port on which you want to serve DP model.

3. `<dp_components_volume>` - directory on your host where you can mount DeepPavlov downloaded components dir.
Most of DeepPavlov models use downloadable components (pretrained model pickles, embeddings...) which are downloaded from our
servers. To prevent downloading components (some of them are quite heavy) each time you run Docker image for specific DP
config, you can make this mount. If you do it, DeepPavlov will store in this dir components downloaded during the first 
launch of any DP config, so during the further launches DP will use components from the mounted folder. We recommend to
use one `<dp_components_volume>` for all models because some of them can use same components. DeepPavlov will automatically
manage downloaded components for all configs in this folder.

4. `<host_venv_dir>` - directory on your host where you can mount DeepPavlov Python virtual environments dir. Each DP
config uses its own set of Python dependencies, which are installed each time you run Docker image for specific DP
config. To prevent it you can make this mount. In this case virtual environment for each `<dp_config_name>` will be saved
in the `<host_venv_dir>/<dp_config_name>` dir for subsequent reuse with this config. Same model config can use different
dependencies in different DeepPavlov versions so be sure not to use same `<host_venv_dir>` for different DP version images.

5. `<dp_version>` - DeepPavlov release ID. Omit to use run latest DP release.

After model initiate, follow url `http://127.0.0.1:<your_port>` in your browser to get Swagger for model API and endpoint reference.

### Example:
1. This will run Docker container with [NER Ontonotes](http://docs.deeppavlov.ai/en/master/features/overview.html#ner-model-docs)
on your host in CPU mode: 

```
docker run -e CONFIG=ner_ontonotes -p 5555:5000 \
    -v ~/my_dp_components:/root/.deeppavlov \
    -v ~/my_dp_envs:/venv \
    deeppavlov/base-cpu
```

2. Follow `http://127.0.0.1:5555` URL in your browser to get Swagger with model API info;

3. Model env located in `~/my_dp_envs/ner_ontonotes`, downloadable components in `~/my_dp_components` 
(contents of this dir is managed by DeepPavlov).