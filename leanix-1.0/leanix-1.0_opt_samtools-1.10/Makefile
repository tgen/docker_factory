.PHONY: build_and_push load_or_build_and_push save

MAKEFILE_PATH           := $(abspath $(lastword $(MAKEFILE_LIST)))
DOCKER_REGISTRY         ?= localhost:5000
IMAGE_NAME              ?= $$(basename $$(dirname ${MAKEFILE_PATH}) )
IMAGE_ARCH              ?= sandybridge
IMAGE_TAG               ?= ${DOCKER_REGISTRY}/${IMAGE_NAME}:${IMAGE_ARCH}
LOCAL_IMAGE_DIR         ?= /archive/docker_factory/
LOCAL_SOURCES_SERVER    ?= localhost:8000

default: build_and_push

build_and_push: Dockerfile
	docker build \
		--pull \
		--network=host \
		--build-arg DOCKER_REGISTRY=${DOCKER_REGISTRY} \
		--build-arg IMAGE_ARCH=${IMAGE_ARCH} \
		--build-arg LOCAL_SOURCES_SERVER=${LOCAL_SOURCES_SERVER} \
		-t ${IMAGE_TAG} \
		.
	docker push ${IMAGE_TAG} 

load_or_build_and_push:
	( docker load < ${LOCAL_IMAGE_DIR}/${IMAGE_NAME}_${IMAGE_ARCH}.tar && \
	  docker build \
		--pull \
		--network=host \
		--build-arg DOCKER_REGISTRY=${DOCKER_REGISTRY} \
		--build-arg IMAGE_ARCH=${IMAGE_ARCH} \
		--build-arg LOCAL_SOURCES_SERVER=${LOCAL_SOURCES_SERVER} \
		-t ${IMAGE_TAG} \
		. )
	docker push ${IMAGE_TAG}
	
save:
	docker save ${IMAGE_TAG} > ${LOCAL_IMAGE_DIR}/${IMAGE_NAME}_${IMAGE_ARCH}.tar