Scripts and notebooks in this package should be run with python 3 (they have been tested with python 3.7). The main dependencies are:
- scipy
- numpy
- pandas
- uproot (version 4)
- scikit-learn
- xgboost

## e/g cluster energy correction and resolution study
### Preprocessing
The preprocessing script `scripts/matching.py` takes as input HGCAL TPG ntuples and produces pandas dataframes in HDF files. It is selecting gen particles reaching the HGCAL and matching them with reconstructed clusters.

As it can take some time to run on all events, this script is associated with a batch launcher script `scripts/batch.py`, which launches jobs to run on multiple input files. This launcher script is meant to run on the LLR batch, but could easily be adapted to any other batch system.

An example of configuration file for `batch.py` is provided in `scripts/batch_matching_autoencoder_sigdriven_210430_cfg.py`

### Energy correction and resolution notebook
The dataframes produced at the preprocessing step are used in the notebook `notebooks/electron_photon_calibration_autoencoder_210430.ipynb`. This notebook is performing the following:
- Derive layer weight correction factors with 0PU **unconverted** photons
- Derive $\eta$ dependent linear energy correction (this is an additive correction) with 200PU electrons
- Produce energy scale and resolution plots, in particular differentially vs  $|\eta|$ and $p_T$

## Electron vs PU discrimination
### Preprocessing
Electron preprocessed files produced in the previous step are used here as well. Only PU events need to be preprocessed now. This is very similar to the electron and photon preprocessing, except that no matching is performed, and energy corrections previously derived are applied to PU clusters.

The PU preprocessing script is `scripts/clusters2hdf.py` and the associated batch launcher is `scripts/batch_nomatching.py`. An example of config file is provided in `scripts/batch_nomatching_pu_for_id_autoencoder_sigdriven_210430_cfg.py`.

### BDT hyperparameters tuning notebook
It is important to note that trigger rates, which is the ultimate metric, require a lot of statistics. Given the size of the available neutrino gun or MinBias samples, the full statistics of these samples need to be used to produce final rate plots. Which means that a lot of attention should be put on the control of the overtraining of our BDTs, since they will be applied on events used to train them.

The notebook `notebooks/electron_pu_bdt_tuning_autoencoder_210430.ipynb` is meant to find the set of hyperparameters to be used later, with focus on the limitation of overtraining rather than optimal performance. This hyperparameter tuning is currently done by hand, and some automatization could be implemented.  XGBOOST is used to train the BDTs. This notebook is doing the following:
- Scan of L1 and L2 regularization parameters. 
- Scan of the learning rate. 
- Scan of the tree depth. 
- Checking the behaviour as a function of the number of boosting steps. 
- Checking overtraining with a final set of hyperparameters

### BDT final training notebook
To be continued...
