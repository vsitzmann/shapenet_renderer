This is a compact implementation of a batched OBJ- and PLY-renderer in blender. The inspiration was drawn
from the "Stanford Shapenet Renderer". This code can be used to render datasets such as the ones used in the
"Scene Representation Networks" paper.

It assumes blender < 2.8, as it uses the blender-internal renderer.

To render a batch of ply files in parallel, use the "find" command in conjunction with xargs:

    find ~/Downloads/02691156/ -name *.ply -print0 | xargs -0 -n1 -P1 -I {} blender --background --python shapenet_spherical_renderer.py -- --output_dir /tmp --mesh_fpath {} --num_observations 50 --sphere_radius 1 --device=cpu --mode=train

A docker image that contains the blender dependencies and utilizes gpu resources for rendering (CUDA enabled gpus) can be built using the following command:
```
docker build -f docker/Dockerfile . -t shapenet_renderer:latest
```

After building the docker image, you can use it in interactive mode as follows:
```
docker run -it --gpus all --volume {LOCAL_DATASET_PATH}:{VOLUME_DATASET_PATH} \ 
    --entrypoint /bin/bash shapenet_renderer:latest
```
and within the docker container:
```
find {VOLUME_DATASET_PATH}/02691156/ -name *.ply -print0 | xargs -0 -n1 -P1 -I {} blender --background -E CYCLES --python shapenet_spherical_renderer.py -- --output_dir /tmp --mesh_fpath {} --num_observations 50 --sphere_radius 1 --device=cuda --mode=train
```