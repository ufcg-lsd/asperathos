
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## Unreleased

## Draco - 06-05-2020
### Added
- Tox configuration used to check tests coverage
- Component health check endpoint
- Node selector feature
- Termination grace period option
- Cost-based Monitor plugin
- Visualizer dashboard for execution cost data
- Validation to trigger up and down parameters
- Support for stream-kubejobs Monitor plugin
### Changed
- Removed completely prints from all components
- Implemented new docker compose format
- Defined light as default theme for Grafana dashboards
- Added min and max replicas to the set of arguments passed to Monitor
- Made config_id argument optional
- Migrated components to python 3.7
- Removed old `kubejobs` files from Monitor
### Fixed

- Duplicated logs on logging module

#### Manager
- Bug on config_id retrieval
- Bug in get activated cluster method
- Flask update issue
- Added retries when job is not found
 
#### Monitor
- Timestamp handling on job report

#### Controller
- Bug on getting a new controller from the plugin service
- Bug on metric source information handling

#### Visualizer
- Fixed default time range

### Security
- Bumped urllib3 from 1.22 to 1.24.2
## Chameleon -10-10-2019
### Added
- Detailed job status
- Complete job report
- Deleting old job's asynchronously
- Integration tests for scalability and job report
### Changed
- The following components are now compatible with python3:
      - Manager
      - Visualizer
- Quickstart updated, now with docker-compose
### Fixed
- Monitor component sleep time was fixed
- Monitor is now presenting the report properly
## Bootes - 16-08-2019
### Added
- New plugin architecture that allows for the addition of custom plugins for the following components:
   - Monitor
   - Manager
   - Visualizer
   - Controller
- Plugin persistence for the following components:
   - Monitor
   - Manager
   - Visualizer
   - Controller
- PID scaling strategy on the Controller component
- New tests for `kubejobs` plugin at Asperathos Monitor

### Changed
- Refactoring of the `kubejobs` plugin of the Manager component

###  Removed
- Unused legacy plugins that are not supported at the moment
### This are the tags you should update the changelog
-   `Added` for new features.
-   `Changed` for changes in existing functionality.
-   `Deprecated` for soon-to-be removed features.
-   `Removed` for now removed features.
-   `Fixed` for any bug fixes.
-   `Security` in case of vulnerabilities.


