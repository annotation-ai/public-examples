# For build OpenCV and GoCV
FROM golang:1.19.3-alpine AS builder

ENV OPENCV_VERSION "4.6.0"
# Produce dynamic libraries (.dll, .so, .dylib)
ENV BUILD_SHARED_LIBS "ON"

RUN apk add --no-cache ca-certificates
RUN apk add --no-cache git build-base musl-dev alpine-sdk cmake clang clang-dev make gcc g++ libc-dev linux-headers bash

# Build OpenCV
RUN mkdir /tmp/opencv
WORKDIR /tmp/opencv
RUN wget -O opencv.zip https://github.com/opencv/opencv/archive/${OPENCV_VERSION}.zip
RUN unzip opencv.zip
RUN wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/${OPENCV_VERSION}.zip
RUN unzip opencv_contrib.zip
RUN mkdir /tmp/opencv/opencv-${OPENCV_VERSION}/build

WORKDIR /tmp/opencv/opencv-${OPENCV_VERSION}/build
RUN cmake \
    -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D BUILD_SHARED_LIBS=${BUILD_SHARED_LIBS} \
    -D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv/opencv_contrib-${OPENCV_VERSION}/modules \
    -D BUILD_DOCS=OFF \
    -D BUILD_EXAMPLES=OFF \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_opencv_java=NO \
    -D BUILD_opencv_python=NO \
    -D BUILD_opencv_python2=NO \
    -D BUILD_opencv_python3=NO \
    -D WITH_JASPER=OFF \
    -D WITH_TBB=ON \
    -D OPENCV_GENERATE_PKGCONFIG=ON \
    ..
RUN make -j$(nproc)
RUN make install
RUN rm -rf /tmp/opencv

# Build the GoCV project
WORKDIR /build
COPY . .
RUN go build main.go

# For runtime
# alpine:3.16's size is around 2.5MB
FROM alpine:3.16

RUN apk add --no-cache ca-certificates libstdc++

ENV PKG_CONFIG_PATH /usr/local/lib/pkgconfig
ENV LD_LIBRARY_PATH /usr/local/lib
ENV CGO_CPPFLAGS -I/usr/local/include
ENV CGO_CXXFLAGS "--std=c++1z"
# Output of `pkg-config --cflags --libs --static opencv4` in builder
ENV CGO_LDFLAGS "-I/usr/local/include/opencv4 -L/usr/local/lib -lopencv_gapi -lopencv_stitching -lopencv_aruco -lopencv_barcode -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_dnn_objdetect -lopencv_dnn_superres -lopencv_dpm -lopencv_face -lopencv_freetype -lopencv_fuzzy -lopencv_hfs -lopencv_img_hash -lopencv_intensity_transform -lopencv_line_descriptor -lopencv_mcc -lopencv_quality -lopencv_rapid -lopencv_reg -lopencv_rgbd -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_superres -lopencv_optflow -lopencv_surface_matching -lopencv_tracking -lopencv_highgui -lopencv_datasets -lopencv_text -lopencv_plot -lopencv_videostab -lopencv_videoio -lopencv_wechat_qrcode -lopencv_xfeatures2d -lopencv_shape -lopencv_ml -lopencv_ximgproc -lopencv_video -lopencv_xobjdetect -lopencv_objdetect -lopencv_calib3d -lopencv_imgcodecs -lopencv_features2d -lopencv_dnn -lopencv_flann -lopencv_xphoto -lopencv_photo -lopencv_imgproc -lopencv_core -ldl -lm -lpthread -lrt"

COPY --from=builder /usr/local/lib /usr/local/lib
COPY --from=builder /usr/local/include/opencv4 /usr/local/include/opencv4
COPY --from=builder /build/main /usr/local/bin
ENTRYPOINT ["main"]
