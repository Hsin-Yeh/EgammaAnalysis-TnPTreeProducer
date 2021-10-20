# My Notes 

Below I list some relevant files for implementing the highPtID. There will be three repositories with several files each

# Step 1: Create Ntuples

[Twiki TnP step1](https://twiki.cern.ch/twiki/bin/view/CMSPublic/Step1TagAndProbe)

Here we create the ntuples for later fitting. Two repository is included. 

### Repo1: EgammaAnalysis-TnPTreeProducer

https://github.com/Hsin-Yeh/EgammaAnalysis-TnPTreeProducer/commit/c69375f02361fe213e1ef7c75ebbf40405dadefb

#### TnPTreeProducer_cfg.py

- This is the main config. Change the cuts and HLT triggers according to the analysis
- The cut could be minimum, since individual cuts could be added in step2 fitting code
- [Egamma recommended HLT for TnP](https://twiki.cern.ch/twiki/bin/view/CMS/EgHLTRunIISummary)

#### egmPhotonIDModules_cff.py
- Store the id modules 

#### egmTreesContent_cff.py
- Store the output variables for step2 fitting 
- These variables are important if you made a loose cut at step1 ntuples, and would like to study different working points at step2.

### Repo2: RecoEgamma/PhotonIdentification

https://github.com/Hsin-Yeh/cmssw/commit/a1d6727bd9420e1735e75f7015068f6b258ee3a4#diff-793d9de606f085f5b335e649d93856aefa28da36e50bc9bb7511bb9f3dbac144

#### cutBasedPhotonID_tools_highPtID.py
- Define the ID compositions

#### PhoAnyPFIso_highPtID.cc
- PhoIsolation correction
- Needs scram b

#### cutBasedPhotonID_highPtID_cff.py
- Cut values
- EA correction file path

#### data/Fall17/*_V2_highPtID.txt
- effective area
- kappa
- alpha

# Step 2: Fitting

Here we do the fitting

### Repo3: egm_tnp_analysis

https://github.com/Hsin-Yeh/egm_tnp_analysis

#### etc/config/settings_highPtID.py

- This is the file for setting the working points you want to study
- New working points, cuts could be added easily in this config file




# EgammaAnalysis-TnPTreeProducer

Package of the EGamma group to produce Tag-and-Probe trees

## Overview of branches

| Branch                                     | release            | tnpEleIDs          | tnpPhoIDs          | tnpEleTrig         | tnpEleReco         | purpose                                |
| ------------------------------------------ | ------------------ |:------------------:|:------------------:|:------------------:|:------------------:|:--------------------------------------:|
|                                            |                    | *miniAOD*          | *miniAOD*          | *miniAOD*          | *AOD*              |                                        |
| [RunIIfinal](../../tree/RunIIfinal)        | CMSSW\_10\_2       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Run II analysis                        |
| [RunIIfinal](../../tree/RunIIfinal)        | CMSSW\_10\_6       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Run II analysis using ultra-legacy     |
| [CMSSW\_11\_X\_Y](../../tree/CMSSW_11_X_Y) | CMSSW\_11          | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :white_check_mark: | Development for Run III (experimental) |

Note: because of a dataformat CMSSW\_10\_6 can only be used for ultra-legacy samples, and CMSSW\_10\_2 should be used for the rereco samples.

## Available tuples

### ReReco 2016, 2017 and 2018
If you do not need changes to the default code, you can simply use existing flat tag and probe trees, avalaible for both 2016, 2017 and 2018 (RunIIfinal branch):
```
ls /eos/cms/store/group/phys_egamma/tnpTuples/tomc/2020-06-09/*/merged/
```
These inlcude the tnpEleTrig, tnpEleIDs and tnpPhoIDs trees produced with the RunIIfinal branch.
*Main change with respect to the 2020-02-28 production is the inclusion of some additional branches, e.g. the leptonMva's*

### ReReco 2016, 2017 and 2018 - L1 matched
In case you need L1 matching for the measurement of doubleEle HLT triggers, you can use the tnpEleTrig trees found in:
```
ls /eos/cms/store/group/phys_egamma/tnpTuples/tomc/2020-03-03/*/merged/*L1matched.root
```

### UL2017 and UL2018
For ultra-legacy  we have tnpEleTrig, tnpEleIDs and tnpPhoIDs trees available at:
```
ls /eos/cms/store/group/phys_egamma/tnpTuples/tomc/2020-05-20/*/merged/
```


## To produce new tuples
### 1a. Install for rereco (CMSSW\_10\_2\_X with X=10 or higher, works for 2016, 2017 and 2018 data/MC)

```
cmsrel CMSSW_10_2_22
cd CMSSW_10_2_22/src
cmsenv
git clone -b RunIIfinal https://github.com/tomcornelis/EgammaAnalysis-TnPTreeProducer EgammaAnalysis/TnPTreeProducer
scram b -j8
```

### 1b. Install for ultra-legacy (CMSSW\_10\_6\_X, works for UL2017 and UL2018 data/MC)

```
cmsrel CMSSW_10_6_13
cd CMSSW_10_6_13/src
cmsenv
git clone -b RunIIfinal https://github.com/tomcornelis/EgammaAnalysis-TnPTreeProducer EgammaAnalysis/TnPTreeProducer
scram b -j8
```


### 2. Try-out
You can find the cmsRun executable in EgammaAnalysis/TnPTreeProducer/python:
```
cmsRun TnPTreeProducer_cfg.py isMC=True doTrigger=True era=2018
```
Check [TnPTreeProducer\_cfg.py](python/TnPTreeProducer_cfg.py) for all available options. Update the code if you need to implement custom-made recipes.

Test files can be defined in [python/etc/tnpInputTestFiles\_cff.py](python/etc/tnpInputTestFiles_cff.py)
If you update the code, you can use the ./runTests.py script in the test directory to check for new differences in the 2016, 2017 and 2018 test files.

### 3. Submit jobs
Check in EgammaAnalysis/TnPTreeProducer//crab the tnpCrabSubmit.py script to submit your jobs using crab

## To make a pull request to this repository
1. On github fork the package https://github.com/cms-analysis/EgammaAnalysis-TnPTreeProducer
2. Add the remote
```
git remote add username-push git@github.com:username/EgammaAnalysis-TnPTreeProducer.git
```
3. push commits to fork and then standard pull request process
```
git push username-push branchname
```

## Adding new workingpoints
You can add new electron workingpoints in [python/egmElectronIDModules\_cff.py](python/egmElectronIDModules_cff.py) and new photon workingpoints
in [python/egmPhotonIDModules\_cff.py](python/egmPhotonIDModules_cff.py). Each new workingpoint added in these python config fragments will
add a new "passing<WP>" boolean in the electron and photon trees respectively. Of course, one can also choose to simply add a variable in
[python/egmTreesContent\_cff.py](python/egmTreesContent\_cff.py), which might be preferred for MVA variables when you want to have the
flexibility to explore different workingpoints: you can simply put a cut on these variable in the egm\_tnp\_analysis package.
