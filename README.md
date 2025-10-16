# Build-docker-sif-image-for-HLAHD

### In COMPUTE1
```bash
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

# Build the Docker image on the cluster
bsub -G compute-oncology -q oncology-interactive -Is \
  -a 'docker_build(jinglunli/hlahd_for_mhchammer)' -- \
  --tag jinglunli/hlahd_for_mhchammer:1.7.1 \
  --tag jinglunli/hlahd_for_mhchammer:latest \
  /scratch1/fs1/mgriffit/allen/hlahd_build
```

### In COMPUTE2
```bash
# Build the `.sif` file on Compute2
apptainer build /storage1/fs1/mgriffit/Active/griffithlab/adhoc/allen/mhc-hammer/hlahd.sif \
  docker://docker.io/jinglunli/hlahd_for_mhchammer:1.7.1

# Quick smoke tests
apptainer exec /storage1/fs1/mgriffit/Active/griffithlab/adhoc/allen/mhc-hammer/hlahd.sif bowtie2 --version
```




update to add the line:
# file: Dockerfile
FROM jinglunli/hlahd_for_mhchammer:1.7.1

# just add the missing env variable — no rebuild of R or apt layers
ENV HLAHD_VERSION=1.7.1



bsub -G compute-oncology -q oncology-interactive -Is \
  -a 'docker_build(jinglunli/hlahd_for_mhchammer)' -- \
  --tag jinglunli/hlahd_for_mhchammer:1.7.1_fixed \
  .
  
