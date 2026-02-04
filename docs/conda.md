# conda.md - dev guide for working with conda

## Environment Info

conda info --envs                      # List all environments with paths
conda info                             # General conda installation info
conda env list                         # Same as conda info --envs
which python                           # Python executable path
conda list                             # All packages in current environment
conda list --show-channel-urls         # Shows where packages came from
conda list --explicit                  # Full URLs of all packages for exact reproduction
conda list --revisions                 # History of environment changes
conda list --json                      # Package list as JSON for scripting
conda info -a                          # Comprehensive system information
conda info --json                      # System info as JSON
echo $CONDA_PREFIX                     # Path to current environment
echo $CONDA_SHLVL                      # Environment stack level

## Environment Management

conda create --name myenv                              # Create new conda environment
conda create --name myenv python=3.11                  # Create new environment with specific Python version
conda create -n myenv python=3.11 numpy pandas scipy   # Create with multiple packages (faster)
conda activate myenv                                   # Activate an environment
conda deactivate                                       # Deactivate current environment
conda remove --name myenv --all                        # Delete an environment completely
conda rename --name old --new new                      # Rename an environment (conda 24.11+)
conda env create -f environment.yml                    # Create env from .yml file
conda env create -f environment.yml --name custom-name # Create with different name
conda env update --file environment.yml                # Update env from .yml file
conda env update -f environment.yml --prune            # Update and remove packages not in file
conda create --clone myenv --name newenv               # Clone an existing environment
conda create --prefix ./env                            # Create env in local folder (good for projects)
conda activate --stack env2                            # Stack environments (CLI tools only, not Python packages)
conda create --platform osx-64 --name test python      # Create for different platform
conda create -n myenv --file explicit.txt              # Create from explicit lockfile

## Package Management

conda install numpy                    # Install a package
conda install numpy=1.24.3             # Install specific version
conda install "numpy>=1.24,<2.0"       # Install with version constraints
conda install "numpy~=1.24.0"          # Compatible release (matches 1.24.x)
conda install conda-forge::pandas      # Install from specific channel (preferred syntax)
conda install -c conda-forge pandas    # Install from channel (older syntax)
conda install --dry-run package        # Preview changes without installing
conda update numpy                     # Update a specific package
conda update --all                     # Update all packages in environment
conda update -n base conda             # Update conda itself (important!)
conda remove scipy                     # Uninstall a package
conda search tensorflow                # Search for package and versions
conda search tensorflow --info         # Detailed package information
conda search "scikit*"                 # Search with wildcards
pip install package --upgrade-strategy only-if-needed # Safer pip usage in conda env

## Version Specifications

conda install numpy=1.24.3=py311h50dd0cd_0 # Exact version with build string
conda install "numpy=1.24.*|1.25.*"        # OR conditions
conda list --export > packages.txt         # Export for pip-style recreation
conda install --revision 8                 # Roll back to specific revision

## Environment Export/Import

conda env export > environment.yml     # Full environment spec with versions and channels
conda env export --from-history        # Only explicitly requested packages (cross-platform)
conda env export --from-history --file portable.yml # Save portable spec
conda env export --no-builds > env.yml # Omit build strings for flexibility
conda list --explicit > lockfile.txt   # Exact reproduction on same platform
conda export > environment.yaml        # New export command (conda 25.x)
conda export --format=json > env.json  # Export as JSON

## Channel Management

conda config --add channels conda-forge      # Add channel (highest priority)
conda config --append channels conda-forge   # Add channel (lowest priority)
conda config --remove channels defaults      # Remove channel
conda config --show channels                 # View current channels
conda config --set channel_priority strict   # Strict channel priority (recommended)
conda config --set channel_priority flexible # Default priority mode
conda config --set channel_priority disabled # No channel priority

## Configuration

conda config --show                                  # All conda configuration settings
conda config --show-sources                          # Where config values come from
conda config --validate                              # Validate configuration files
conda config --get channel_priority                  # Show specific setting
conda config --describe channel_priority             # Explain configuration option
conda config --set auto_activate_base false          # Don't auto-activate base
conda config --env --set channel_priority strict     # Environment-specific config
conda config --system --add channels conda-forge     # System-wide config (needs admin)
conda config --remove-key channels                   # Remove configuration key entirely
conda config --set pip_interoperability_enabled true # Experimental pip integration

## Performance Optimization

conda config --set solver libmamba        # Use fast libmamba solver (default in 23.10+)
conda config --set repodata_threads 4     # Parallel repository downloads
conda config --set verify_threads 1       # Package verification threads
conda config --set safety_checks disabled # Skip verification (use cautiously)

## Debugging and Diagnostics

conda install package -v               # Verbose output
conda install package -vv              # INFO logging level
conda install package -vvv             # DEBUG logging level
conda install package -vvvv            # TRACE logging level
export CONDA_VERBOSITY=3               # Set verbosity via environment variable
export CONDA_DEBUG=1                   # Enable debug mode
conda info --json | jq '.'             # Full conda info as structured JSON

## Query and Dependency Tools (libmamba/mamba)

conda repoquery depends numpy               # Show package dependencies
conda repoquery depends -t pandas           # Recursive dependency tree
conda repoquery whoneeds python             # Find reverse dependencies
conda repoquery search "tensorflow*"        # Search with patterns
conda repoquery search numpy -p osx-arm64   # Query specific platform
conda repoquery depends --pretty tensorflow # Pretty formatted output

## Cleanup and Maintenance

conda clean --all                      # Remove all: index, locks, tarballs, packages, sources
conda clean --packages                 # Remove unused packages only
conda clean --tarballs                 # Remove downloaded tarballs
conda clean --index-cache              # Remove index cache
conda clean --dry-run --all            # Preview what would be deleted
conda clean -f                         # Force removal without confirmation
conda list --revisions                 # Show environment history
conda install --revision 2             # Restore to previous revision

## Advanced Environment Creation

conda create --prefix /path/to/env              # Create at specific location
conda create -n myenv --strict-channel-priority # Override global setting
conda create -n myenv --no-default-packages     # Skip default packages
conda env create -f environment.yml --force     # Overwrite existing environment

## Pip Integration Best Practices

conda install pip                           # Always install pip via conda first
pip list --format=freeze > requirements.txt # Export pip packages
pip install -r requirements.txt             # Install from requirements
pip freeze > pip-requirements.txt           # Alternative pip export

## Mamba (Fast Alternative)

mamba create -n myenv python=3.11      # Create env with mamba (5-10x faster)
mamba install numpy pandas             # Fast package installation
mamba update --all                     # Fast environment update
mamba repoquery depends numpy          # Query dependencies
mamba repoquery whoneeds python        # Find what needs a package
micromamba create -n myenv python=3.11 # Standalone tool for CI/CD

## Production and Reproducibility

conda-lock -f environment.yml -p linux-64   # Generate lockfile for platform
conda-lock install -n myenv                 # Install from lockfile
conda-lock --update numpy                   # Update specific package in lock
conda list --explicit > production.lock     # Platform-specific exact reproduction
conda create -n prod --file production.lock # Create from explicit file

## Package Pinning

echo "numpy 1.24.*" >> $CONDA_PREFIX/conda-meta/pinned   # Pin package version
echo "scipy ==1.11.2" >> $CONDA_PREFIX/conda-meta/pinned # Exact pin
conda update --all --no-pin                              # Update ignoring pins temporarily

## Offline and Air-gapped Environments

conda config --set offline true               # Enable offline mode
conda install --use-local package             # Install from local cache only
conda install file:///path/to/package.tar.bz2 # Install from local file

## Tips & Best Practices

conda activate base                        # Activate base env explicitly
conda create -n test --dry-run python=3.11 # Test environment creation
conda install --yes package                # Skip confirmation prompts
conda env list | grep myenv                # Check if environment exists
conda list | grep numpy                    # Check if package is installed
conda info --base                          # Show base environment location
conda run -n myenv python script.py        # Run command in env without activating

conda install ipykernel                    # Your environment needs `ipykernel` installed to be visible as a Jupyter kernel. This allows you to select it in a .ipynb file as the Python environment. Refresh VS Code with Cmd+Shift+P → "Developer: Reload Window" and click the Refresh wheel icon if it still doesn't show up.