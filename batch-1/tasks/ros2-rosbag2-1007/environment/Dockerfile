FROM ros:humble

ENV DEBIAN_FRONTEND=noninteractive
SHELL ["/bin/bash", "-c"]

RUN apt-get update && apt-get install -y \
    git \
    python3-colcon-common-extensions \
    ros-humble-ament-cmake \
    ros-humble-ament-cmake-ros \
    ros-humble-ament-cmake-gmock \
    ros-humble-ament-cmake-gtest \
    ros-humble-ament-index-cpp \
    ros-humble-ament-lint-auto \
    ros-humble-ament-lint-common \
    ros-humble-builtin-interfaces \
    ros-humble-keyboard-handler \
    ros-humble-pluginlib \
    ros-humble-rcl \
    ros-humble-rclcpp \
    ros-humble-rcpputils \
    ros-humble-rcutils \
    ros-humble-rmw \
    ros-humble-rmw-implementation \
    ros-humble-rmw-implementation-cmake \
    ros-humble-rmw-fastrtps-cpp \
    ros-humble-rosgraph-msgs \
    ros-humble-rosidl-default-generators \
    ros-humble-rosidl-default-runtime \
    ros-humble-rosidl-runtime-c \
    ros-humble-rosidl-runtime-cpp \
    ros-humble-rosidl-typesupport-cpp \
    ros-humble-rosidl-typesupport-introspection-cpp \
    ros-humble-test-msgs \
    ros-humble-yaml-cpp-vendor \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace

RUN git init /workspace/repo && cd /workspace/repo && \
    git remote add origin https://github.com/ros2/rosbag2.git && \
    git fetch --depth 30 origin 832dea8358fb81fcf3b06b9538ea4ef25ace6bce && \
    git checkout --detach FETCH_HEAD && \
    git submodule update --init --recursive --depth 1 2>/dev/null || true && \
    git remote remove origin && \
    git tag -l | xargs -r git tag -d && \
    git for-each-ref --format='%(refname)' refs/remotes refs/tags 2>/dev/null | xargs -r -n1 git update-ref -d && \
    git reflog expire --expire=now --all && \
    git gc --prune=now --aggressive && \
    rm -rf .git/logs

RUN mkdir -p /ros_ws/src && \
    for pkg in shared_queues_vendor sqlite3_vendor zstd_vendor \
        rosbag2_interfaces rosbag2_storage rosbag2_cpp \
        rosbag2_compression rosbag2_compression_zstd \
        rosbag2_test_common rosbag2_transport; do \
        ln -s /workspace/repo/$pkg /ros_ws/src/$pkg; \
    done

RUN . /opt/ros/humble/setup.sh && cd /ros_ws && \
    colcon build \
        --packages-select shared_queues_vendor sqlite3_vendor zstd_vendor \
            rosbag2_interfaces rosbag2_storage rosbag2_cpp \
            rosbag2_compression rosbag2_compression_zstd rosbag2_test_common \
        --cmake-args -DBUILD_TESTING=OFF -DCMAKE_BUILD_TYPE=RelWithDebInfo \
        2>&1 | tail -60

WORKDIR /workspace/repo

WORKDIR /workspace

RUN echo '#!/bin/bash' > /workspace/run_tests.sh && \
    echo '. /opt/ros/humble/setup.sh' >> /workspace/run_tests.sh && \
    echo '. /ros_ws/install/setup.sh' >> /workspace/run_tests.sh && \
    echo 'cd /ros_ws' >> /workspace/run_tests.sh && \
    echo 'mkdir -p /workspace/test-results' >> /workspace/run_tests.sh && \
    echo 'colcon build --packages-select rosbag2_interfaces rosbag2_transport --cmake-args -DBUILD_TESTING=ON -DCMAKE_BUILD_TYPE=RelWithDebInfo >/dev/null 2>&1' >> /workspace/run_tests.sh && \
    echo '. install/setup.sh' >> /workspace/run_tests.sh && \
    echo 'test -f /ros_ws/build/rosbag2_transport/test_player_stop__rmw_fastrtps_cpp || exit 1' >> /workspace/run_tests.sh && \
    echo 'test -f /ros_ws/build/rosbag2_transport/test_play_services__rmw_fastrtps_cpp || exit 1' >> /workspace/run_tests.sh && \
    echo 'RMW_IMPLEMENTATION=rmw_fastrtps_cpp /ros_ws/build/rosbag2_transport/test_player_stop__rmw_fastrtps_cpp >/dev/null 2>&1 || exit 1' >> /workspace/run_tests.sh && \
    echo 'RMW_IMPLEMENTATION=rmw_fastrtps_cpp /ros_ws/build/rosbag2_transport/test_play_services__rmw_fastrtps_cpp --gtest_output=json:/workspace/test-results/output.json "$@" >/dev/null 2>&1 || true' >> /workspace/run_tests.sh && \
    echo "python3 -c \"import json; f=open('/workspace/test-results/output.json'); d=json.load(f); f.close(); ts=d.pop('testsuites',[]); r={'testsuites':ts}; r.update(d); open('/workspace/test-results/output.json','w').write(json.dumps(r,indent=2))\"" >> /workspace/run_tests.sh && \
    chmod +x /workspace/run_tests.sh

# --- offline-harbor: ensure grading(python3) + patch(patch/git) deps (build-time) ---
RUN (command -v python3 >/dev/null 2>&1 && command -v patch >/dev/null 2>&1 && command -v git >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends python3 patch git ca-certificates) \
    || (apk add --no-cache python3 patch git ca-certificates) \
    || (dnf install -y python3 patch git ca-certificates) \
    || (yum install -y python3 patch git ca-certificates) \
    || true

# --- terminal-bench eval harness deps (tmux + asciinema), baked for offline eval ---
RUN (command -v tmux >/dev/null 2>&1 && command -v asciinema >/dev/null 2>&1) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux asciinema) \
    || (apt-get update && apt-get install -y --no-install-recommends tmux python3-pip && pip3 install --no-cache-dir asciinema) \
    || (apk add --no-cache tmux asciinema) \
    || (dnf install -y tmux asciinema) \
    || (yum install -y tmux asciinema) \
    || true

# --- login-shell PATH: the client execs the verifier via `bash -lc`, which resets PATH from
# /etc/profile and drops the base-image toolchain (go/cargo/...). Bake the built PATH into
# /etc/profile.d so login shells find the toolchain (matches non-login Modal behavior). ---
RUN printf 'export PATH="%s"\n' "$PATH" > /etc/profile.d/zzz_offline_path.sh && chmod 0644 /etc/profile.d/zzz_offline_path.sh
