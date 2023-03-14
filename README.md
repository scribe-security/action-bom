---
title: Bom
sidebar_position: 2
---
# Scribe Github Action for `valint bom`
Scribe offers GitHub Actions for embedding evidence collecting and validated integrity of your supply chain.

Use `valint bom` to collect evidence and generate an SBOM.

Further documentation [Github integration](https://scribe-security.netlify.app/docs/ci-integrations/github/)

## Other Actions
* [bom](action-bom.md), [source](https://github.com/scribe-security/action-bom)
* [verify](action-verify.md), [source](https://github.com/scribe-security/action-verify)
* [installer](action-installer.md), [source](https://github.com/scribe-security/action-installer)
<!-- * [integrity report - action](https://github.com/scribe-security/action-report/README.md) -->

## Bom Action
Action for `valint bom`. <br />
The command allows users to generate and manage evidence collection process.
- CycloneDX SBOM and SLSA provenance evidence support. 
- Generates detailed SBOMs for images, directories, files and git repositories targets.
- Store and manage evidence on Scribe service.
- Attach evidence in your CI, OCI or release service.
- Generate evidence directly from your private OCI registry.
- Extensive SBOM component relation graph including, file to package, file and package to layer, commit history and file to commit relations.
- SBOM including package CPEs and Licensing information.
- Customizable SBOM with environments, labels.
- Customizable SBOM with your required component groups.
- Attach any external reports to your SBOM.
- Generate In-Toto attestation, statement or predicates.
- Support Sigstore keyless verifying as well as [Github workload identity](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect).
- Attach GitHub workflows [environment](https://docs.github.com/en/actions/learn-github-actions/environment-variables) context (git url , commit, workflow, job, run id ..).

> Containerized actions limit's the ability to generate evidence on a target located outside the working directory (directory or git targets). <br />
To overcome the limitation install tool directly - [installer](https://github.com/scribe-security/action-installer/README.md)

### Input arguments
```yaml
  type:
    description: 'Target source type options=[docker,docker-archive, oci-archive, dir, registry, git]'
    default: registry
  target:
    description: 'Target object name format=[<image:tag>, dir:<dir_path>, <git_path>]'
    required: true
  verbose:
    description: 'Log verbosity level [-v,--verbose=1] = info, [-vv,--verbose=2] = debug'
    default: 1
  config:
    description: 'Application config file'
  format:
    description: 'Evidence format, options=[cyclonedx-json cyclonedx-xml attest-cyclonedx-json statement-cyclonedx-json predicate-cyclonedx-json attest-slsa statement-slsa predicate-slsa]'
  output-directory:
    description: 'Output directory path'
    default: ./scribe/valint
  output-file:
    description: 'Output result to file'
  product-key:
    description: 'Custom/project product key'
  label:
    description: 'Custom label'
  env:
    description: 'Custom env'
  filter-regex:
    description: 'Filter out files by regex'
  filter-scope:
    description: 'Filter packages by scope'
  package-type:
    description: 'Select package type'
  package-group:
    description: 'Select package group'
  package-exclude-type:
    description: 'Exclude package type'
  attach-regex:
    description: 'Attach files content by regex'
  force:
    description: 'Force overwrite cache'
    default: false
  attest-config:
    description: 'Attestation config map'
  attest-default:
    description: 'Attestation default config, options=[sigstore sigstore-github x509]'
    default: sigstore-github
  scribe-enable:
    description: 'Enable scribe client'
    default: false
  scribe-client-id:
    description: 'Scribe client id' 
  scribe-client-secret:
    description: 'Scribe access token' 
  context-dir:
    description: 'Context dir' 
  components:
    description: 'Select sbom components groups, options=[metadata layers packages syft files dep commits]'
  oci:
    description: 'Enable OCI store'
    default: false
  oci-repo:
    description: 'Select OCI custom attestation repo'
```

### Output arguments
```yaml
  OUTPUT_PATH:
    description: 'evidence output file path'
```

### Usage
```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
```

## Configuration

Use default configuration path `.valint.yaml`, or provide a custom path using `--config` flag.

See detailed [configuration](docs/configuration.md)

## Attestations 
Attestations allow you to sign and verify your targets. <br />
Attestations allow you to connect PKI-based identities to your evidence and policy management.  <br />

Supported outputs:
- In-toto predicate - Cyclonedx SBOM, SLSA Provenance (unsigned evidence)
- In-toto statements - Cyclonedx SBOM, SLSA Provenance (unsigned evidence)
- In-toto attestations -Cyclonedx SBOM, SLSA Provenance (signed evidence)

Select default configuration using `--attest.default` flag. <br />
Select a custom configuration by providing `cocosign` field in the [configuration](docs/configuration.md) or custom path using `--attest.config`.

See details [In-toto spec](https://github.com/in-toto/attestation)
See details [attestations](docs/attestations.md)

>By default Github actions use `sigstore-github` flow, Github provided workload identities, this will allow using the workflow identity (`token-id` permissions is required).

## Integrations

### Before you begin
See [Github integration](https://scribe-security.netlify.app/docs/ci-integrations/github/)

## Scribe service integration
Scribe provides a set of services to store, verify and manage the supply chain integrity. <br />
Following are some integration examples.

### Usage
```yaml
- name: generate sbom for image
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    scribe-enable: true
    product-key:  ${{ secrets.product-key }}
    scribe-client-id: ${{ secrets.client-id }}
    scribe-client-secret: ${{ secrets.client-secret }}
```

If you are using Github Actions as your Continuous Integration tool (CI), use these instructions to integrate Scribe into your workflows to protect your projects.

<details>
  <summary>  Scribe integrity </summary>

Full workflow example of a workflow, upload evidence on source and image to Scribe. <br />
Verifying the  target integrity on Scribe.

```YAML
name: example workflow

on: 
  push:
    tags:
      - "*"

jobs:
  scribe-evidence-test:
    runs-on: ubuntu-latest
    steps:

      - uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - uses: actions/checkout@v3
        with:
          repository: mongo-express/mongo-express
          ref: refs/tags/v1.0.0-alpha.4
          path: mongo-express-scm

      - name: valint Scm generate bom, upload to scribe
        id: valint_bom_scm
        uses: scribe-security/action-bom@master
        with:
           type: dir
           target: 'mongo-express-scm'
           scribe-enable: true
           product-key:  ${{ secrets.product-key }}
           scribe-client-id: ${{ secrets.client-id }}
           scribe-client-secret: ${{ secrets.client-secret }}

      - name: Build and push remote
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: mongo-express:1.0.0-alpha.4

      - name: valint Image generate bom, upload to scribe
        id: valint_bom_image
        uses: scribe-security/action-bom@master
        with:
           target: 'mongo-express:1.0.0-alpha.4'
           scribe-enable: true
           product-key:  ${{ secrets.product-key }}
           scribe-client-id: ${{ secrets.client-id }}
           scribe-client-secret: ${{ secrets.client-secret }}

      - uses: actions/upload-artifact@v3
        with:
          name: scribe-evidence
          path: |
            ${{ steps.valint_bom_scm.outputs.OUTPUT_PATH }}
            ${{ steps.valint_bom_image.outputs.OUTPUT_PATH }}
```
</details>

## Basic examples
<details>
  <summary>  Public registry image (SBOM) </summary>

Create SBOM for remote `busybox:latest` image.

```YAML
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    format: json
``` 

</details>

<details>
  <summary>  Docker built image (SBOM) </summary>

Create SBOM for image built by local docker `image_name:latest`.

```YAML
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: docker
    target: 'image_name:latest'
    format: json
    force: true
``` 
</details>

<details>
  <summary>  Private registry image (SBOM) </summary>

Create SBOM for image hosted by a private registry.

> `DOCKER_CONFIG` environment will allow the containerized action to access the private registry.

```YAML
env:
  DOCKER_CONFIG: $HOME/.docker
steps:
  - name: Login to GitHub Container Registry
    uses: docker/login-action@v2
    with:
      registry: ${{ env.REGISTRY_URL }}
      username: ${{ secrets.REGISTRY_USERNAME }}
      password: ${{ secrets.REGISTRY_TOKEN }}

  - name: Generate cyclonedx json SBOM
    uses: scribe-security/action-bom@master
    with:
      target: 'scribesecuriy.jfrog.io/scribe-docker-local/stub_remote:latest'
      force: true
```
</details>

<details>
  <summary>  Custom metadata (SBOM) </summary>

Custom metadata added to SBOM.

```YAML
- name: Generate cyclonedx json SBOM - add metadata - labels, envs
  id: valint_labels
  uses: scribe-security/action-bom@master
  with:
      target: 'busybox:latest'
      format: json
      force: true
      env: test_env
      label: test_label
  env:
    test_env: test_env_value
```
</details>


<details>
  <summary> Save as artifact (SBOM, SLSA) </summary>

Using action `OUTPUT_PATH` output argument you can access the generated SBOM and store it as an artifact.

> Use action `output-file: <my_custom_path>` input argument to set a custom output path.

```YAML
- name: Generate cyclonedx json SBOM
  id: valint_json
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    output-file: my_sbom.json
    format: json

- uses: actions/upload-artifact@v2
  with:
    name: scribe-sbom
    path: ${{ steps.valint_json.outputs.OUTPUT_PATH }}

- uses: actions/upload-artifact@v2
  with:
    name: scribe-evidence
    path: scribe/
``` 
</details>

<details>
  <summary> Save provenance statement as artifact (SLSA) </summary>

Using action `OUTPUT_PATH` output argument you can access the generated SLSA provenance statement and store it as an artifact.

> Use action `output-file: <my_custom_path>` input argument to set a custom output path.

```YAML
- name: Generate SLSA provenance statement
  id: valint_slsa_statement
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    format: statement-slsa

- uses: actions/upload-artifact@v2
  with:
    name: provenance
    path: ${{ steps.valint_slsa_statement.outputs.OUTPUT_PATH }}
``` 
</details>

<details>
  <summary> Docker archive image (SBOM) </summary>

Create SBOM for local `docker save ...` output.

```YAML
- name: Build and save local docker archive
  uses: docker/build-push-action@v2
  with:
    context: .
    file: .GitHub/workflows/fixtures/Dockerfile_stub
    tags: scribesecuriy.jfrog.io/scribe-docker-public-local/stub_local:latest
    outputs: type=docker,dest=stub_local.tar

- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: docker-archive
    target: '/GitHub/workspace/stub_local.tar'
``` 
</details>

<details>
  <summary> OCI archive image </summary>

Create SBOM for the local oci archive.

```YAML
- name: Build and save local oci archive
  uses: docker/build-push-action@v2
  with:
    context: .
    file: .GitHub/workflows/fixtures/Dockerfile_stub
    tags: scribesecuriy.jfrog.io/scribe-docker-public-local/stub_local:latest
    outputs: type=oci,dest=stub_oci_local.tar

- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: oci-archive
    target: '/GitHub/workspace/stub_oci_local.tar'
``` 
</details>

<details>
  <summary> Directory target (SBOM) </summary>

Create SBOM for a local directory.

```YAML
- name: Create dir
  run: |
    mkdir testdir
    echo "test" > testdir/test.txt

- name: valint attest dir
  id: valint_attest_dir
  uses: scribe-security/action-bom@master
  with:
    type: dir
    target: 'testdir'
``` 
</details>


<details>
  <summary> Git target (SBOM) </summary>

Create SBOM for `mongo-express` remote git repository.

```YAML
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: git
    target: 'https://github.com/mongo-express/mongo-express.git'
    format: json
``` 

Create SBOM for `my_repo` local git repository.

```YAML

- uses: actions/checkout@v3
  with:
    fetch-depth: 0
    path: my_repo

- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: git
    target: 'my_repo'
    format: json
``` 

</details>

<details>
  <summary> Attest target (SBOM) </summary>

Create and sign SBOM targets. <br />
By default the `sigstore-github` flow is used, GitHub workload identity and Sigstore (Fulcio, Rekor).

>Default attestation config **Required** `id-token` permission access. <br />

```YAML
job_example:
  runs-on: ubuntu-latest
  permissions:
    id-token: write
  steps:
    - name: valint attest
      uses: scribe-security/action-bom@master
      with:
          target: 'busybox:latest'
          format: attest
``` 

</details>

<details>
  <summary> Attest target (SLSA) </summary>

Create and sign SLSA targets. <br />
By default the `sigstore-github` flow is used, GitHub workload identity and Sigstore (Fulcio, Rekor).

>Default attestation config **Required** `id-token` permission access.

```YAML
job_example:
  runs-on: ubuntu-latest
  permissions:
    id-token: write
  steps:
    - name: valint attest
    uses: scribe-security/action-bom@master
    with:
        target: 'busybox:latest'
        format: attest-slsa
``` 
</details>

<details>
  <summary> Verify target (SBOM) </summary>

Verify targets against a signed attestation. <br />

Default attestation config: `sigstore-github` - sigstore (Fulcio, Rekor). <br />
valint will look for both a bom or slsa attestation to verify against.  <br />

```YAML
- name: valint verify
  uses: scribe-security/action-verify@master
  with:
    target: 'busybox:latest'
``` 

</details>

<details>
  <summary> Verify target (SLSA) </summary>

Verify targets against a signed attestation. <br />

Default attestation config: `sigstore-github` - sigstore (Fulcio, Rekor). <br />
Tool will look for sbom or slsa attestation to verify against. <br />

```YAML
- name: valint verify
  uses: scribe-security/action-verify@master
  with:
    target: 'busybox:latest'
    input-format: attest-slsa
``` 

</details>

<details>
  <summary> Attest and verify image target (SBOM) </summary>

Full job example of a image signing and verifying flow.

```YAML
valint-busybox-test:
  runs-on: ubuntu-latest
  permissions:
    contents: read
    packages: write
    id-token: write
  steps:

    - uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: valint attest
      id: valint_attest
      uses: scribe-security/action-bom@master
      with:
          target: 'busybox:latest'
          format: attest
          force: true

    - name: valint verify
      id: valint_verify
      uses: scribe-security/action-verify@master
      with:
          target: 'busybox:latest'

    - uses: actions/upload-artifact@v2
      with:
        name: valint-busybox-test
        path: scribe/valint
``` 

</details>

<details>
  <summary> Attest and verify image target (SLSA) </summary>

Full job example of a image signing and verifying flow.

```YAML
valint-busybox-test:
  runs-on: ubuntu-latest
  permissions:
    contents: read
    packages: write
    id-token: write
  steps:

    - uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: valint attest slsa
      id: valint_attest
      uses: scribe-security/action-bom@master
      with:
          target: 'busybox:latest'
          format: attest-slsa
          force: true

    - name: valint verify attest slsa
      id: valint_verify
      uses: scribe-security/action-verify@master
      with:
          target: 'busybox:latest'
          input-format: attest-slsa

    - uses: actions/upload-artifact@v2
      with:
        name: valint-busybox-test
        path: scribe/valint
``` 

</details>

<details>
  <summary> Attest and verify directory target (SBOM) </summary>

Full job example of a directory signing and verifying flow.

```YAML
valint-dir-test:
  runs-on: ubuntu-latest
  permissions:
    contents: read
    packages: write
    id-token: write
  steps:

    - uses: actions/checkout@v2
      with:
        fetch-depth: 0

    - name: valint attest workdir
      id: valint_attest_dir
      uses: scribe-security/action-bom@master
      with:
          type: dir
          target: '/GitHub/workspace/'
          format: attest
          force: true

    - name: valint verify workdir
      id: valint_verify_dir
      uses: scribe-security/action-verify@master
      with:
          type: dir
          target: '/GitHub/workspace/'
    
    - uses: actions/upload-artifact@v2
      with:
        name: valint-workdir-evidence
        path: |
          scribe/valint      
``` 

</details>

<details>
  <summary> Attest and verify Git repository target (SBOM) </summary>

Full job example of a git repository signing and verifying flow.
> Support for both local (path) and remote git (url) repositories.

```YAML
valint-dir-test:
  runs-on: ubuntu-latest
  permissions:
    contents: read
    packages: write
    id-token: write
  steps:

    - uses: actions/checkout@v3
      with:
        fetch-depth: 0

    - name: valint attest local repo
      id: valint_attest_dir
      uses: scribe-security/action-bom@master
      with:
          type: git
          target: '/GitHub/workspace/my_repo'
          format: attest
          force: true

    - name: valint verify local repo
      id: valint_verify_dir
      uses: scribe-security/action-verify@master
      with:
          type: git
          target: '/GitHub/workspace/my_repo'
    
    - uses: actions/upload-artifact@v3
      with:
        name: valint-git-evidence
        path: |
          scribe/valint      
``` 

</details>



<details>
  <summary> Store evidence on OCI (SBOM,SLSA) </summary>

Store any evidence on any OCI registry. <br />
Support storage for all targets and both SBOM and SLSA evidence formats.

> Use input variable `format` to select between supported formats. <br />
> Write permission to `oci-repo` is required. 

```YAML
valint-dir-test:
  runs-on: ubuntu-latest
  permissions:
    id-token: write
  env:
    DOCKER_CONFIG: $HOME/.docker
  steps:
    - uses: actions/checkout@v3
      with:
        fetch-depth: 0

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY_URL }}
          username: ${{ secrets.REGISTRY_USERNAME }}
          password: ${{ secrets.REGISTRY_TOKEN }}

      - uses: scribe-security/action-bom@dev
        id: valint_attest
        with:
          target: busybox:latest
          force: true
          format: attest
          oci: true
          oci-repo: ${{ env.REGISTRY_URL }}/attestations    
``` 

Following command can be used to verify a target over the OCI store.
```yaml
valint verify busybox:latest -f --oci --oci-repo=$REGISTRY_URL/attestations
```

> Use `--input-format` to select between supported formats. <br />
> Read permission to `oci-repo` is required. 

</details>

<details>
  <summary> Install valint (tool) </summary>

Install valint as a tool
```YAML
- name: install valint
  uses: scribe-security/action-installer@master

- name: valint run
  run: |
    valint --version
    valint bom busybox:latest
``` 
</details>

## .gitignore
Recommended to add output directory value to your .gitignore file.
By default add `**/scribe` to your `.gitignore`.

