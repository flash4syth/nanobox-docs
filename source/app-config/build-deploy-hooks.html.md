---
title: Build & Deploy Hooks
---

## The Build & Deploy Process

*Coming Soon*

## Build Hooks
There are two main phases of the build process to consider when defining build hooks. These two phases are handled by your [engine](/engines-images/).

1. **Prepare** - This is the phase where runtime executables are downloaded and installed in the build node.
2. **Build** - This is when commands are run to build/compile your code. The most common of which is running dependency managers.

Build hooks are configured in the the `code.build` section of your boxfile.yml.

#### Important Things to Know About Build Hooks
- [Environment variables](/app-config/environment-variables/) are **not** available during the build.
- Build hooks do not have access to data services.
- Code is fully writable.

### before_prepare
`before_prepare` hooks run before runtime executables are downloaded and installed. The most common use for these hooks is to download executables your engine normally doesn't install.

```yaml
code.build:
  before_prepare:
    - 'echo "I am getting ready"'
    - 'curl -sSf https://static.rust-lang.org/rustup.sh | sh'
```

### after_prepare
`after_prepare` hooks run after runtime executables are downloaded and installed.

```yaml
code.build:
  after_prepare:
    - 'echo "Good to go"'
```

### before_build
`before_build` hooks run after runtime executables are downloaded and installed, but before code is built/compiled.

```yaml
code.build:
  before_build:
    - 'cargo build --release'
    - 'echo Ready to be built'
```

### after_build
`after_build` hooks run after code is built/compiled.

```yaml
code.build:
  after_build:
    - 'echo Built and ready to go'
```

## Deploy Hooks
Deploy hooks run after your code is built, packaged, and pushed to your new nodes.

#### Important Things to Know About Deploy Hooks
- [Environment variables](/app-config/environment-variables/) are available to deploy hooks.
- Deploy hooks have access to running data services.
- Code is writable during [transform hooks](#transform), but read-only once transforms are complete.

### transform
`transform` hooks run on all web and worker nodes just before their filesystems are locked down and made read-only. Any code modification dependent on environment variables must be run as a transform.

```yaml
code.deploy:
  transform:
    - 'if [ "$ENV" = "prod" ]; then mv config-prod.yml config.yml; fi'
```

### before_deploy
`before_deploy` hooks run on a new node before requests are routed to new nodes. These are unique to each component, so Nanobox needs to know on which each will run. In multi-node components, these only run on a single node.

```yaml
code.deploy:
  before_deploy:
    web.site:
      - 'bundle exec rake clear-cache'
      - 'echo "I am ready to go"'
    worker.jobs:
      - 'bundle exec check-queue'
```

### before\_deploy\_all
`before_deploy_all` hooks are the same as `before_deploy` hooks, but they run on all new nodes in a multi-node component. These are useful when you need to modify the contents of [nonpersistent writable directories](/app-config/nonpersistent-writable-dirs/).

```yaml
code.deploy:
  before_deploy_all:
    web.site:
      - 'bundle exec prime-local-cache'
      - 'bundle exec register-host'
```

### after_deploy
`after_deploy` hooks run on a new node after requests are routed to new nodes. These are unique to each component, so Nanobox needs to know on which each will run. In multi-node components, these only run on a single node.

```yaml
code.deploy:
  after_deploy:
    web.site:
      - 'bundle exec rake prime-cache'
      - 'echo "I am working"'
    worker.jobs:
      - 'bundle exec start-queue'
```

### after\_deploy\_all
`after_deploy_all` hooks are the same as `after_deploy` hooks, but they run on all new nodes in a multi-node component. These are useful when you need to modify the contents of [nonpersistent writable directories](/app-config/nonpersistent-writable-dirs/).

```yaml
code.deploy:
  after_deploy_all:
    web.site:
      - 'bundle exec prime-local-cache'
      - 'bundle exec register-host'
```