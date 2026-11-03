# LX Recipe: <LX_TITLE_HERE>

TODO: add information that is useful for instructors

# How to Create an LX Recipe

The LX recipe contains components of the exercise that the student should
not need to change and are effectively hidden from view. There are a few
files here that will require some attention in the creation of the learning
experience

## Dependencies

Specify any `apt` or `python` dependencies needed in the 
[dependencies-apt.txt](./dependencies-apt.txt) and 
[dependencies-py3.txt](./dependencies-py3.txt) respectively. 

## Base Image

You need to specify a `BASE_IMAGE` in the [Dockerfile](./Dockerfile). This
choice defines what is already in the docker image and can be used in the 
running of the exercise.

Reasonable choices could be:

 - [`dt-ros-commons`](https://github.com/duckietown/dt-ros-commons): likely
a good choice if you would like to use ROS in a standalone fashion (i.e., 
your exercise includes all the nodes that are needed)
 - [`dt-core`](https://github.com/duckietown/dt-core): a good choice if you 
would like to use one or more of the nodes defined in the packages 
in the `dt-core` repository. 
 - [`dt-commons`](https://github.com/duckietown/dt-commons): likely a good choice
if the LX is pure python and doesn't require ROS.
 - [`dt-machine-learning-base-environment`](https://github.com/duckietown/dt-machine-learning-base-environment):
the base image for machine learning LXs. Check its README for what it includes on
each architecture.

The [Dockerfile](./Dockerfile) builds the `packages/` directory as a ROS 1
(catkin) workspace, which works with `dt-ros-commons` and `dt-core`. The other
two images do not include ROS, so if you use them, remove the `catkin build`
step from the Dockerfile and make the packages importable instead, by adding
this line after the `COPY` instructions:

```
ENV PYTHONPATH=${REPO_PATH}/packages
```

See [lx-recipe-planning](https://github.com/duckietown/lx-recipe-planning) for
an example.

ROS 2 LXs (based on `dt-ros2-commons`, built with colcon) need a different
Dockerfile: a ROS 2 variant of this template is planned.

## Duckiematrix 

By default, each LX is compatible with the [Duckiematrix](https://docs.duckietown.com/ente/devmanual-duckiematrix/intro.html).
See the [README in the LX template](https://github.com/duckietown/template-lx/blob/ente/README.md)
for more details on how to deploy this capability through the `dts code` API. 
You can define the exact configuration of the Duckiematrix environment in the 
[assets/duckiematrix](./assets/duckiematrix) directory. For more details see
[the Duckiematrix manual](https://docs.duckietown.com/ente/devmanual-duckiematrix/intro.html).

## Interactive tools and the noVNC desktop (optional)

Most LXs do not need a separate desktop: interactive tools such as sliders,
plots and image views work well inside the notebooks with
[`ipywidgets`](https://ipywidgets.readthedocs.io) (add `ipywidgets` to
[dependencies-py3.txt](./dependencies-py3.txt)).

[Dockerfile.vnc](./Dockerfile.vnc) builds the desktop that learners open in the
browser with `dts code vnc`. It is based on `dt-gui-tools`, which provides ROS 1
tools such as RViz and `rqt`, so it is only useful for ROS 1 LXs that need them.
To add your own launchers, icons or scripts to the desktop, put them under
[assets/vnc](./assets/vnc) and copy them in `Dockerfile.vnc`, for example:

```
COPY --from=recipe assets/vnc/root/Desktop/. /root/Desktop/
COPY --from=recipe assets/vnc/root/icons/. /root/.icons/
COPY --from=recipe assets/vnc/usr/bin/. /usr/bin/
```

If your LX does not use the desktop, delete `Dockerfile.vnc` and `assets/vnc/`
(`dts code vnc` then opens the plain Duckietown desktop) and remove the noVNC
paragraph from the LX README.

