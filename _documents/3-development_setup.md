---
title: "Development Setup"
url: "/development-setup/"
---
# Development Setup

## Setting up Fleet threat integrations

 1. Install Docker, have a functional Elasticsearch + Kibana instance.
 2. Add the following lines to `kibana.dev.yaml`
<br/>
```
xpack.fleet.agents.enabled: true
xpack.fleet.agents.elasticsearch.host: "http://host.docker.internal:9200"
xpack.fleet.agents.fleet_server.hosts: ["http://host.docker.internal:8220"]
```
 3. Clone [integrations](https://github.com/elastic/integrations) and [elastic-package](https://github.com/elastic/elastic-package)
 4. run `make-build` in elastic-package
 5. run `elastic-package stack update` in integrations
 6. confirm profile setup by running `elastic-package profiles list` to see a default profile
 7. update `~/.elastic-package/profiles/default/stack/snapshot.yml` to [this snippet](https://gist.github.com/nkhristinin/73d17b59f52822e194d243732c251d34)
 8. run Elasticsearch with `yarn es snapshot  -E xpack.security.authc.api_key.enabled=true -E xpack.security.enabled=true -E indices.id_field_data.enabled=true --license trial`
 9. run Kibana with `start-kibana`
 10. run fleet & elastic-agent with `elastic-package stack up -d --services fleet-server,elastic-agent -v --version=8.0.0-SNAPSHOT`

You can now install a threat integration using the Integrations UI in Kibana. Once you enable a threat integration and assign the default agent policy, you will see that a `logs_ti-*` index is created and indicator events are visible in Kibana UI.

## Setting up filebeat threatintel integrations
 1. start elasticsearch and kibana
 2. clone [beats](https://github.com/elastic/beats)
 3. edit beats/x-pack/filebeat/filebeat.yml
    - to include your elasticsearch username and password under output.elasticsearch
    - to include kibana username and password under setup.kibana

_example_
```
// beats/x-pack/filebeat/filebeat.yml
output.elasticsearch:
hosts: ["localhost:9200"]
username: "ece"
password: "testtest"

setup.kibana:  
host: "localhost:5601"
username: "ece"
password: "testtest"
```

 4. run `mage build` to build filebeat
 5. run `./filebeat modules enable threatintel` and edit `modules.d/threatintel.yml` to ensure that all values for `enabled` are `true`
 6. run `./filebeat setup -E setup.dashboards.directory=build/kibana` to setup dashboards (while kibana is running)
 7. run `./filebeat -e` to start filebeat

You will now see that a filebeat index is created and indicator events are visible in kibana.

## Running Security Solution API Integration tests

 1. cd to `kibana`, check out the branch you'd like to test, and run `start-bootstrap`
 2. If you haven't done so once, build platform plugins with `node scripts/build_kibana_platform_plugins`
 3. run `node scripts/functional_tests --config x-pack/test/detection_engine_api_integration/security_and_spaces/config.ts`
 4. if you'd like to run only a subsection of available tests in the `detection_engine_api_integration` directory, go through `index.ts` file in each subdirectory and comment out the files you'd like to skip.

## Running Security Solution jest tests
 1. cd to `kibana/x-pack`
 2. run `node scripts/jest.js` and append the directory or file name to run the tests you'd like (such as `node scripts/jest.js percolator` to run all files in the percolator directory)
 3. you can also add `.skip` to `describe` or `it` blocks to skip suites / specs in the targeted files
 4. use `testing-library` instead of `enzyme` if you are writing new tests


## Running Security Solution cypress tests
1. cd to `kibana/x-pack/plugins/security_solution`
2. run `yarn cypress:open-as-ci` to start the cypress UI
3. choose any tests you'd like to run from there

## Useful security_solution scripts

```

# Elastic aliases
export KIBANA_HOME=$HOME/Workspace/kibana
export PLUGIN_NAME=security_solution
export CASE_PLUGIN_NAME=plugins/case
export NODE_OPTIONS=--max-old-space-size=8192
export filebeat_location=$HOME/go/src/github.com/elastic/beats/x-pack/filebeat
export backport_location=$HOME/.backport/repositories/elastic/kibana

# Start elasticsearch
alias kb-es='cd ${KIBANA_HOME} && yarn es snapshot -E path.data=${KIBANA_HOME}/../data -E xpack.security.authc.api_key.enabled=true --license trial'

# Start kibana
alias start-kibana='cd $KIBANA_HOME && yarn start --no-base-path'

# Start bootstrap
alias start-bootstrap='cd $KIBANA_HOME && yarn kbn bootstrap'

# Start typecheck
alias start-type-check='cd $KIBANA_HOME && node scripts/type_check.js --project x-pack/tsconfig.json'

# Start ftr
alias start-ftr-server='cd ${KIBANA_HOME}/x-pack && node scripts/functional_tests_server.js'
alias start-ftr='cd ${KIBANA_HOME}/x-pack && node scripts/functional_test_runner' 

# Start lint
alias start-lint='cd $KIBANA_HOME && node scripts/eslint.js'
alias start-lint-siem='cd $KIBANA_HOME && node scripts/eslint.js x-pack/plugins/$PLUGIN_NAME'
alias start-lint-case='cd $KIBANA_HOME && node scripts/eslint.js x-pack/plugins/case'

# Start unit tests
alias start-jest='cd $KIBANA_HOME/x-pack && node scripts/jest.js $PLUGIN_NAME'
alias start-jest-case='cd $KIBANA_HOME/x-pack && node scripts/jest.js $CASE_PLUGIN_NAME'

# Start unit tests watch
alias start-jest-watch='cd $KIBANA_HOME/x-pack && node scripts/jest.js $PLUGIN_NAME --watch'
alias start-jest-watch-size='cd $KIBANA_HOME/x-pack && node --max-old-space-size=8192 --optimize-for-size  --max_old_space_size=8192 --optimize_for_size scripts/jest.js $PLUGIN_NAME --watch --max_new_space_size=8192'
alias start-jest-watch-case='cd $KIBANA_HOME/x-pack && node scripts/jest.js $CASE_PLUGIN_NAME --watch'

# Start unit tests coverage
alias start-jest-coverage='cd $KIBANA_HOME/x-pack && node scripts/jest.js $PLUGIN_NAME --coverage'
alias start-jest-coverage-case='cd $KIBANA_HOME/x-pack && node scripts/jest.js $CASE_PLUGIN_NAME --coverage'

# Start generation
alias start-bean-gen='cd $KIBANA_HOME/x-pack/plugins/$PLUGIN_NAME && node scripts/generate_types_from_graphql.js'

# Run cyclic dependencies test
alias start-deps-check='cd ${KIBANA_HOME}/x-pack/plugins/${PLUGIN_NAME} && node scripts/check_circular_deps.js'

# Start api integration tests
alias start-integration='cd $KIBANA_HOME && node scripts/functional_tests --config x-pack/test/api_integration/config.js'

# Start cypress open
alias start-cypress-open='cd $KIBANA_HOME/x-pack/plugins/$PLUGIN_NAME && yarn cypress:open'

# Start cypress run
alias start-cypress-run='cd $KIBANA_HOME/x-pack/plugins/$PLUGIN_NAME && yarn cypress:run'

# Start cypress ci
alias start-cypress-ci='cd $KIBANA_HOME/x-pack/plugins/$PLUGIN_NAME && yarn cypress:run-as-ci'

# Test all
alias start-test-all='start-bean-gen && start-i18n-check && start-type-check && start-lint && start-lint-case && start-jest && start-jest-case'

# Start a translation check
alias start-i18n-check='cd $KIBANA_HOME && node scripts/i18n_check --ignore-missing'

# Backport alias
alias start-backport='cd $KIBANA_HOME && node scripts/backport'

# Delete node_modules and caches
alias start-burn-the-world='cd $KIBANA_HOME && git clean -fdx -e \.idea\/ -e config\/ && rm -rf node_modules && yarn cache clean'

# Sync kibana
alias sync-kibana='cd $KIBANA_HOME && git checkout main && git fetch upstream && git merge upstream/main && git push origin main'

# Branch Update with Upstream master
alias bru='git fetch upstream && git pull -r upstream main && git status'
```