# Build-docker-sif-image-for-HLAHD


### In COMPUTE1 
LSB_DOCKER_LOGIN_ONLY=1 \
  bsub -G compute-oncology -q oncology-interactive -Is -a 'docker_build' -- .



# Make a minimal build context
mkdir -p /scratch1/fs1/mgriffit/allen/hlahd_build/bin

# Copy the Dockerfile you made
cp /storage1/fs1/mgriffit/Active/griffithlab/adhoc/allen/mhc-hammer/Dockerfile \
   /scratch1/fs1/mgriffit/allen/hlahd_build/

# Copy payloads that the Dockerfile expects:
#   bin/hlahd.1.7.1.tar.gz  and  bin/update.dictionary.alt.sh
cp /storage1/fs1/mgriffit/Active/griffithlab/adhoc/allen/mhc-hammer/bin/hlahd.1.7.1.tar.gz \
   /scratch1/fs1/mgriffit/allen/hlahd_build/bin/
cp /storage1/fs1/mgriffit/Active/griffithlab/adhoc/allen/mhc-hammer/bin/update.dictionary.alt.sh \
   /scratch1/fs1/mgriffit/allen/hlahd_build/bin/



   1) Build the Docker image on the cluster (your bsub pattern)

Use the same pattern you used for CIBERSORTx, just point the path to your minimal build dir:

# (Option A) Name in the -a app, then pass tags:
bsub -G compute-oncology -q oncology-interactive -Is \
  -a 'docker_build(jinglunli/hlahd_for_mhchammer)' -- \
  --tag jinglunli/hlahd_for_mhchammer:1.7.1 \
  --tag jinglunli/hlahd_for_mhchammer:latest \
  /scratch1/fs1/mgriffit/allen/hlahd_build



2) Export the built Docker image to a tarball

Still inside the docker_build session after it finishes:

docker images | grep jinglunli/hlahd
docker save jinglunli/hlahd:1.7.1 -o /scratch1/fs1/mgriffit/allen/hlahd_1.7.1.tar

3) Convert the tarball to .sif with Apptainer/Singularity (outside of Docker, using Compute2)

Back in a normal shell on the cluster (no docker_build needed):

apptainer build /scratch1/fs1/mgriffit/allen/hlahd_1.7.1.sif \
  docker-archive:///scratch1/fs1/mgriffit/allen/hlahd_1.7.1.tar




  
