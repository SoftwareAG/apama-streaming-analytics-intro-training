# Copyright (c) 2018 Software AG, Darmstadt, Germany and/or its licensors
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
# file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the
# License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
# either express or implied. 
# See the License for the specific language governing permissions and limitations under the License.

ARG APAMA_VERSION=10.3
ARG APAMA_BUILDER=store/softwareag/apama-builder:${APAMA_VERSION}
ARG APAMA_IMAGE=store/softwareag/apama-correlator:${APAMA_VERSION}

# Use the build environment
FROM ${APAMA_BUILDER} as builder

# Copy project assets into build environment
COPY --chown=1724:1724 . ${APAMA_WORK}/Project

# Handle the type repository if the project is using Digital Event Services. You will need to ensure that a copy of the type repository is checked out inside your project directory at the time of building.
RUN if [ -e ${APAMA_WORK}/Project/TypeRepository ]; then rm -rf ${SAG_HOME}/common/DigitalEventServices/TypeRepository; cp -a ${APAMA_WORK}/Project/TypeRepository ${SAG_HOME}/common/DigitalEventServices/TypeRepository; fi

# Deploy the apama project
RUN engine_deploy --outputDeployDir ${APAMA_WORK}/Project_deployed ${APAMA_WORK}/Project

# Add any additional build steps you may have, such as building plugins, here
# RUN ...

# Final image is based on the runtime base image
FROM ${APAMA_IMAGE}

# Copy deployed project from the build environment
COPY --chown=1724:1724 --from=builder ${APAMA_WORK}/Project_deployed ${APAMA_WORK}/Project_deployed

# Copy the DES type repository
RUN rm -rf ${SAG_HOME}/common/DigitalEventServices/TypeRepository
COPY --chown=1724:1724 --from=builder ${SAG_HOME}/common/DigitalEventServices/TypeRepository ${SAG_HOME}/common/DigitalEventServices/TypeRepository

# Add copies for any additional build artifacts created in the build environment
# COPY --from=builder ...

WORKDIR ${APAMA_WORK}

# Run the deployed project
CMD ["correlator", "--config", "Project_deployed", "--logfile", "/dev/stdout"]

