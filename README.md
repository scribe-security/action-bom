---
sidebar_label: "Bom"
title: Scribe GitHub Action for `valint bom`
sidebar_position: 2
toc_min_heading_level: 2
toc_max_heading_level: 5
---

Scribe offers the use of GitHub Actions to enable the embedding of evidence collection and integrity validation into your pipeline as a way to help secure your software supply chain.

`valint bom` is used to collect evidence and generate an SBOM.

Further documentation **[GitHub integration](../../../integrating-scribe/ci-integrations/github)**.

### Bom Action
The command allows users to generate sbom and third party evidence.
- CycloneDX 1.5 SBOM support. 
- Generates detailed SBOMs for images, directories, files and git repositories targets.
- Store and manage evidence on Scribe service.
- Attach evidence to any OCI registry.
- Generate evidence directly from your private OCI registry.
- Extensive SBOM component relation graph including, file to package, file and package to layer, commit history and file to commit relations.
- SBOM including package CPEs and Licensing information.
- Customizable SBOM with environments, labels.
- Customizable SBOM with your required component groups.
- Attach any external reports to your SBOM.
- Signing - Generate In-Toto Attestation.
- Support Sigstore keyless verifying as well as **[GitHub workload identity](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)**.
- Attach GitHub workflows **[environment](https://docs.github.com/en/actions/learn-environment-variables)** context (git url , commit, workflow, job, run id ..).

> Containerized actions limit's the ability to generate evidence on a target located outside the working directory (directory or git targets). <br />
To overcome the limitation install tool directly - **[installer](https://github.com/scribe-security/actions/tree/master/installer)**.

### Input arguments
```yaml
  target:
    description: Target object name format=[<image:tag>, <dir path>, <git url>]
    required: true
  attach-regex:
    description: Attach files content by regex
  attest-config:
    description: Attestation config path
  attest-default:
    description: Attestation default config, options=[sigstore sigstore-github x509 x509-env kms pubkey]
  author-email:
    description: Set author email
  author-name:
    description: Set author name
  author-phone:
    description: Set author phone
  base-image:
    description: Base image for the target
  ca:
    description: x509 CA Chain path
  cert:
    description: x509 Cert path
  components:
    description: Select sbom components groups, options=[metadata layers packages syft files dep commits base_image]
  crl:
    description: x509 CRL path
  crl-full-chain:
    description: Enable Full chain CRL verfication
  depth:
    description: Git clone depth
  disable-crl:
    description: Disable certificate revocation verificatoin
  filter-regex:
    description: Filter out files by regex
  filter-scope:
    description: Filter packages by scope
  force:
    description: Force overwrite cache
  format:
    description: Evidence format, options=[json statement attest]
  git-auth:
    description: 'Git repository authentication info, [format: ''username:password'']'
  git-branch:
    description: Git branch in the repository
  git-commit:
    description: Git commit hash in the repository
  git-tag:
    description: Git tag in the repository
  key:
    description: x509 Private key path
  kms:
    description: Provide KMS key reference
  oci:
    description: Enable OCI store
  oci-repo:
    description: Select OCI custom attestation repo
  package-exclude-type:
    description: Exclude package type, options=[ruby python javascript java dpkg apk rpm go dotnet r rust binary sbom nix conan alpm cocoapods swift dart elixir php erlang github portage haskell kernel wordpress lua bitnami terraform]
  package-group:
    description: Select package group, options=[index install all]
  package-type:
    description: Select package type, options=[ruby python javascript java dpkg apk rpm go dotnet r rust binary sbom nix conan alpm cocoapods swift dart elixir php erlang github portage haskell kernel wordpress lua bitnami terraform]
  pass:
    description: Private key password
  payload:
    description: path of the decoded payload
  platform:
    description: Select target platform, examples=windows/armv6, arm64 ..)
  provenance:
    description: Include SLSA Provenance evidence
  pubkey:
    description: Public key path
  skip-confirmation:
    description: Skip Sigstore Confirmation
  supplier-email:
    description: Set supplier email
  supplier-name:
    description: Set supplier name
  supplier-phone:
    description: Set supplier phone
  supplier-url:
    description: Set supplier url
  cache-enable:
    description: Enable local cache
  config:
    description: Configuration file path
  deliverable:
    description: Mark as deliverable, options=[true, false]
  env:
    description: Environment keys to include in evidence
  gate-name:
    description: Policy Gate name
  gate-type:
    description: Policy Gate type
  input:
    description: Input Evidence target, format (\<parser>:\<file> or \<scheme>:\<name>:\<tag>)
  label:
    description: Add Custom labels
  level:
    description: Log depth level, options=[panic fatal error warning info debug trace]
  log-context:
    description: Attach context to all logs
  log-file:
    description: Output log to file
  output-directory:
    description: Output directory path
    default: ./scribe/valint
  output-file:
    description: Output file name
  pipeline-name:
    description: Pipeline name
  predicate-type:
    description: Custom Predicate type (generic evidence format)
  product-key:
    description: Product Key
  product-version:
    description: Product Version
  scribe-client-id:
    description: Scribe Client ID (deprecated)
  scribe-client-secret:
    description: Scribe Client Token
  scribe-disable:
    description: Disable scribe client
  scribe-enable:
    description: Enable scribe client (deprecated)
  scribe-url:
    description: Scribe API Url
  structured:
    description: Enable structured logger
  timeout:
    description: Timeout duration
  verbose:
    description: Log verbosity level [-v,--verbose=1] = info, [-vv,--verbose=2] = debug
```

### Output arguments
```yaml
  OUTPUT_PATH:
    description: 'evidence output file path'
```

### Usage
Containerized action can be used on Linux runners as following
```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@v2.0.1
  with:
    target: 'busybox:latest'
```

Composite Action can be used on Linux or Windows runners as following
```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom-cli@v2.0.1
  with:
    target: 'hello-world:latest'
```

> Use `master` instead of tag to automatically pull latest version.


### 1. Obtain a Scribe Hub API Token
1. Sign in to [Scribe Hub](https://app.scribesecurity.com). If you don't have an account you can sign up for free [here](https://scribesecurity.com/scribe-platform-lp/ "Start Using Scribe For Free").

2. Create a API token in [Scribe Hub > Settings > Tokens](https://app.scribesecurity.com/settings/tokens). Copy it to a safe temporary notepad until you complete the integration.

:::note Important
The token is a secret and will not be accessible from the UI after you finalize the token generation. 
:::

### 2. Add the API token to Github secrets

Set your Scribe Hub API token in Github with a key named SCRIBE_TOKEN as instructed in *GitHub instructions](https://docs.github.com/en/actions/security-guides/encrypted-secrets/ "GitHub Instructions")

### 3. Instrument your build scripts

#### Usage

```yaml
name:  scribe_github_workflow

on: 
  push:
    tags:
      - "*"

jobs:
  scribe-sign-verify:
    runs-on: ubuntu-latest
    steps:
      - uses: scribe-security/action-bom@master
        with:
          target: [target]
          format: [attest, statement]
          scribe-client-secret: ${{ secrets.SCRIBE_TOKEN }}

      - uses: scribe-security/action-verify@master
        with:
          target: [target]
          input-format: [attest, statement]
          scribe-client-secret: ${{ secrets.SCRIBE_TOKEN }}
```

### Configuration
If you prefer using a custom configuration file instead of specifying arguments directly, you have two choices. You can either place the configuration file in the default path, which is `.valint.yaml`, or you can specify a custom path using the `config` argument.

For a comprehensive overview of the configuration file's structure and available options, please refer to the CLI configuration documentation.

### Attestations 
Attestations allow you to sign and verify your targets. <br />
Attestations allow you to connect PKI-based identities to your evidence and policy management.  <br />

Supported outputs:
- In-toto statements CycloneDX SBOM (unsigned evidence).
- In-toto attestations CycloneDX SBOM (signed evidence).

Select default configuration using `--attest.default` flag. <br />
Select a custom configuration by providing `cocosign` field in the **[configuration](../configuration)** or custom path using `--attest.config`.
Scribe uses the **cocosign** library we developed to deal with digital signatures signing and verification.

* See details of in-toto spec **[here](https://github.com/in-toto/attestation)**.
* See details of what attestations are and how to use them **[here](../attestations)**.

>By default GitHub actions use `sigstore-github` flow, GitHub provided workload identities, this will allow using the workflow identity (`token-id` permissions is required).


### Storing Keys in Secret Vault

GitHub exposes secrets from its vault using environment variables, you may provide these environment as secret to Valint.

> Paths names prefixed with `env://[NAME]` are read from the environment matching the name.

<details>
  <summary> GitHub Secret Vault </summary>

X509 Signer enables the utilization of environments for supplying key, certificate, and CA files in order to sign and verify attestations. It is commonly employed in conjunction with Secret Vaults, where secrets are exposed through environments.

>  path names prefixed with `env://[NAME]` are extracted from the environment corresponding to the specified name.


For example the following configuration and Job.

Configuration File, `.valint.yaml`
```yaml
attest:
  default: "" # Set custom configuration
  cocosign:
    signer:
        x509:
            enable: true
            private: env://SIGNER_KEY
            cert: env://SIGNER_CERT
            ca: env://COMPANY_CA
    verifier:
        x509:
            enable: true
            cert: env://SIGNER_CERT
            ca: env://COMPANY_CA
```
Job example
```yaml
name:  github_vault_workflow

on: 
  push:
    tags:
      - "*"

jobs:
  scribe-sign-verify:
    runs-on: ubuntu-latest
    steps:
        uses: scribe-security/action-bom@master
        with:
          target: busybox:latest
          format: attest
        env:
          SIGNER_KEY: ${{ secrets.SIGNER_KEY }}
          SIGNER_CERT: ${{ secrets.SIGNER_CERT }}
          COMPANY_CA:  ${{ secrets.COMPANY_CA }}

        uses: scribe-security/action-verify@master
        with:
          target: busybox:latest
          input-format: attest
        env:
          SIGNER_CERT: ${{ secrets.SIGNER_CERT }}
          COMPANY_CA:  ${{ secrets.COMPANY_CA }}
```

</details>

### Mapping the DOCKER_CONFIG Environment Variable

When using a job's Docker daemon to pull private images, you might need to log in to your private registry before running **valint**. To ensure that the containerized action `action-bom` can access your private image, set `DOCKER_CONFIG` to a directory in your workflow that is accessible for mapping.

Below is an example configuration:

```yaml
env:
  DOCKER_CONFIG: ${{ github.workspace }}/.docker

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
      target: 'scribesecurity/example:latest'
```

Alternatively, you can use uncontainerized actions directly:

```yaml
- name: Login to GitHub Container Registry
  uses: docker/login-action@v2
  with:
    registry: ${{ env.REGISTRY_URL }}
    username: ${{ secrets.REGISTRY_USERNAME }}
    password: ${{ secrets.REGISTRY_TOKEN }}

- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom-cli@master
  with:
    target: 'scribesecurity/example:latest'
    format: json
```


### Running action as non root user
By default, the action runs in its own pid namespace as the root user. You can change the user by setting specific `USERID` and `USERNAME` environment variables.

```YAML
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    format: json
  env:
    USERID: 1001
    USERNAME: runner
``` 

<details>
  <summary> Non root user with HIGH UID/GID </summary>
By default, the action runs in its own pid namespace as the root user. If the user uses a high UID or GID, you must specify all the following environment variables. You can change the user by setting specific `USERID` and `USERNAME` variables. Additionally, you may group the process by setting specific `GROUPID` and `GROUP` variables.

```YAML
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    format: json
  env:
    USERID: 888000888
    USERNAME: my_user
    GROUPID: 777000777
    GROUP: my_group
``` 
</details>

### Platform-Specific Image Handling
The Valint tool is compatible with both Linux and Windows images. Set the desired platform using the 'platform' field in your configuration:

```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: hello-world:latest
    platform: linux/amd64
    format: json
```

Docker is configured by default to pull images matching the runner's platform. For analyzing images across different platforms, you need to pull the image from the registry and specify the platform.

```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    target: registry:hello-world:latest
    platform: windows/amd64
    format: json
```

### Windows Runner Compatibility
> On Windows Github runners, containerized actions are currently not supported. It's recommended to use CLI actions in such cases.

```yaml
- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom-cli@master
  with:
    target: hello-world:latest
    format: json
```

### Basic examples
<details>
  <summary>  Attach sbom to product </summary>

Create SBOM for remote `busybox:latest` image and attach it to a specific product version.

```YAML
- name: Generate cyclonedx json SBOM attached to a product
  uses: scribe-security/action-bom@master
  with:
    target: 'busybox:latest'
    product-key: my_product
    product-version: 3
    format: json
``` 

</details>

<details>
  <summary>  Set Git sbom as deliverable artifact </summary>

Create SBOM for Deliverable  `mongo-express/mongo-express` Git repository.

```YAML
- name: Generate cyclonedx json SBOM for a deliverable Git repo
  uses: scribe-security/action-bom@master
  with:
    target: git:https://github.com/mongo-express/mongo-express.git
    product-key: my_product
    product-version: 3
    deliverable: true
    format: json
``` 

</details>


<details>
  <summary>  Public registry image </summary>

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
  <summary>  Docker built image </summary>

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
  <summary>  Private registry image </summary>

Create SBOM for image hosted by a private registry.

> `DOCKER_CONFIG` environment will allow the containerized action to access the private registry.

```YAML
env:
  DOCKER_CONFIG: ${{ github.workspace }}/.docker
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
      target: 'scribesecurity/example:latest'
      
```
</details>

<details>
  <summary>  SBOM with SLSA Provenance </summary>

Generate an SBOM and SLSA Provenance for a container image.

```yaml
- name: Generate SBOM with SLSA Provenance
  uses: scribe-security/action-bom@dev
  with:
    target: 'hello-world:latest'
    verbose: 2
    format: statement
    provenance: true
```

</details>

<details>
  <summary>  SBOM with Base Image Analysis </summary>

Generate an SBOM for a container image while analyzing its base image.

```yaml
- name: Generate SBOM with Base Image Analysis
  uses: scribe-security/action-bom@dev
  with:
    target: 'hello-world:latest'
    verbose: 2
    format: statement
    base-image: alpine:latest
```

</details>

<details>
  <summary>  SBOM with Additional Inputs </summary>

Create an SBOM for a container image by including supplementary inputs like SARIF reports, Git repositories, or additional images.

```yaml
- name: Generate SBOM with Additional Inputs
  uses: scribe-security/action-bom@dev
  with:
    target: 'hello-world:latest'
    verbose: 2
    format: statement
    input: |
      sarif:test.json,
      git:git:https://github.com/mongo-express/mongo-express.git,
      alpine:latest
```

</details>



<details>
  <summary>  Custom metadata </summary>

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
  <summary>  NTIA Custom metadata </summary>

Custom NTIA metadata added to SBOM.

```YAML
- name: Generate cyclonedx json SBOM - add NTIA metadata
  id: valint_ntia
  uses: scribe-security/action-bom@master
  with:
      target: 'busybox:latest'
      format: json
      force: true
      author-name: bob
      author-email: bob@company.com
      author-phone: 000
      supplier-name: alice
      supplier-url: company2.com
      supplier-email: alice@company2.com
      supplier-phone: 001
```
</details>


<details>
  <summary> Save evidence as artifact </summary>

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

- uses: actions/upload-artifact@v4
  with:
    name: scribe-sbom
    path: ${{ steps.valint_json.outputs.OUTPUT_PATH }}

- uses: actions/upload-artifact@v4
  with:
    name: scribe-evidence
    path: scribe/
``` 
</details>

<details>
  <summary> Docker archive image </summary>

Create SBOM for local `docker save ...` output.

```YAML
- name: Build and save local docker archive
  uses: docker/build-push-action@v2
  with:
    context: .
    file: .GitHub/workflows/fixtures/Dockerfile_stub
    tags: scribesecurity/example:latest
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

Create SBOM for the local OCI archive.

```YAML
- name: Build and save local oci archive
  uses: docker/build-push-action@v2
  with:
    context: .
    file: .GitHub/workflows/fixtures/Dockerfile_stub
    tags: scribesecurity/example:latest
    outputs: type=oci,dest=stub_oci_local.tar

- name: Generate cyclonedx json SBOM
  uses: scribe-security/action-bom@master
  with:
    type: oci-archive
    target: '/GitHub/workspace/stub_oci_local.tar'
``` 
</details>

<details>
  <summary> Directory target </summary>

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
  <summary> Git target </summary>

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
  <summary> Attest target </summary>

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
  <summary> Attest File evidence </summary>

Create and sign file as evidence. <br />
By default the `sigstore-github` flow is used, GitHub workload identity and Sigstore (Fulcio, Rekor).

>Default attestation config **Required** `id-token` permission access.

```YAML
job_example:
  runs-on: ubuntu-latest
  permissions:
    id-token: write
  steps:
    - name: valint attest
    uses: scribe-security/action-evidence@master
    with:
        target: './file.go'
        format: attest
``` 
</details>

<details>
  <summary> Verify target </summary>

Verify targets against a signed attestation. <br />

Default attestation config: `sigstore-github` - Sigstore (Fulcio, Rekor). <br />
Valint will look for an SBOM describing the image to verify against.  <br />

```YAML
- name: valint verify
  uses: scribe-security/action-verify@master
  with:
    target: 'busybox:latest'
``` 

</details>

<details>
  <summary> Verify Policy flow - image target (Signed SBOM) </summary>

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

      - uses: actions/upload-artifact@v4
        with:
          name: valint-busybox-test
          path: scribe/valint
``` 

</details>

<details>
  <summary> Verify Policy flow - Directory target (Signed SBOM) </summary>

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
      
      - uses: actions/upload-artifact@v4
        with:
          name: valint-workdir-evidence
          path: |
            scribe/valint      
``` 

</details>


<details>
  <summary> Verify Policy flow - Git repository target (Signed SBOM) </summary>

Full job example of a git repository signing and verifying flow.
> Support for both local (path) and remote git (URL) repositories.

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
      
      - uses: actions/upload-artifact@v4
        with:
          name: valint-git-evidence
          path: |
            scribe/valint      
``` 

</details>

<details>
  <summary> Attest and verify evidence on OCI SBOM </summary>

Store any evidence on any OCI registry. <br />
Support storage for all targets and both SBOM formats.

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

      - uses: scribe-security/action-bom@master
        id: valint_attest
        with:
          target: busybox:latest
          force: true
          format: attest
          oci: true
          oci-repo: ${{ env.REGISTRY_URL }}/attestations    
``` 

Following actions can be used to verify a target over the OCI store.
```yaml
      - name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY_URL }}
          username: ${{ secrets.REGISTRY_USERNAME }}
          password: ${{ secrets.REGISTRY_TOKEN }}

      - uses: scribe-security/action-verify@master
        id: valint_attest
        with:
          target: busybox:latest
          input-format: attest
          oci: true
          oci-repo: ${{ env.REGISTRY_URL }}/attestations   
```
> Read permission to `oci-repo` is required. 

</details>

<details>
  <summary> Install Valint (tool) </summary>

Install Valint as a tool
```YAML
- name: install valint
  uses: scribe-security/action-installer@master

- name: valint run
  run: |
    valint --version
    valint bom busybox:latest
``` 
</details>

### Alternative evidence stores

> You can learn more about alternative stores **[here](https://scribe-security.netlify.app/docs/integrating-scribe/other-evidence-stores)**.

<details>
  <summary> <b> OCI Evidence store </b></summary>

Valint supports both storage and verification flows for `attestations` and `statement` objects utilizing OCI registry as an evidence store.

Using OCI registry as an evidence store allows you to upload, download and verify evidence across your supply chain in a seamless manner.

Related flags:
* `oci` Enable OCI store.
* `oci-repo` - Evidence store location.

### Before you begin

Evidence can be stored in any accusable registry.
* Write access is required for upload (generate).
* Read access is required for download (verify).

You must first login with the required access privileges to your registry before calling Valint.
For example, using `docker login` command or `docker/login-action` action.

### Usage

```yaml
name:  scribe_github_workflow

on: 
  push:
    tags:
      - "*"

jobs:
  scribe-sign-verify:
    runs-on: ubuntu-latest
    steps:

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.my_registry }}
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name:  Generate evidence step
        uses: scribe-security/action-bom@master
        with:
          target: [target]
          format: [attest, statement]
          oci: true
          oci-repo: [oci_repo]

      - name:  Verify policy step
        uses: scribe-security/action-verify@master
        with:
          target: [target]
          input-format: [attest, statement]
          oci: true
          oci-repo: [oci_repo]
```
</details>


## .gitignore
It's recommended to add output directory value to your .gitignore file.
By default add `**/scribe` to your `.gitignore`.

## Other Actions
* [bom](action-bom.md), [source](https://github.com/scribe-security/action-bom)
* [slsa](action-slsa.md), [source](https://github.com/scribe-security/action-slsa)
* [evidence](action-evidence.md), [source](https://github.com/scribe-security/action-evidence)
* [verify](action-verify.md), [source](https://github.com/scribe-security/action-verify)
* [installer](action-installer.md), [source](https://github.com/scribe-security/action-installer)
