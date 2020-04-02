
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## Unreleased

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


