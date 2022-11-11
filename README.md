# Key4HEP_first_experience

This is a diary of my first attempt to use Key4HEP.

## November 10, 2022

- "google" (actually using ecosia) key4hep, first hit yields https://key4hep.github.io/key4hep-doc/
-  log in to lxplus.cern.ch
- create new directory key4hep
- start to follow instructions on https://key4hep.github.io/key4hep-doc/ :
   - source /cvmfs/sw.hsf.org/key4hep/setup.sh 
       - yields: /cvmfs/sw.hsf.org/key4hep/setup.sh:export:4: not valid in this context: key4hep-stack/2022-10-30
       - probably need to use bash ? => yes, when using bash, the setup scripts seems to work
   - since I don't want to run the CLIC simulation & reconstruction, but a kinematic fit example, I clone 
       - the MarlinKinfit tutorial:  git clone https://github.com/ILDAnaSoft/MarlinKinfitTutorial.git
       - MarlinKinfitProcessors : git clone https://github.com/iLCSoft/MarlinKinfitProcessors.git
       - the tutorial has local processors to be compiled, maybe start with one of the normal example processors from MarlinKinfitProcessors, WW5CFit
   - get some data file. 
     - Quick: scp one from DESY ... immediately exceeds disk space :(
     - **TODO:** 
       - **ask André whether there is a similar easy access to local grid like /pnfs ...**
       - **ask for group afs space or so**
     - for now: try on DESY NAF...:
       - mkdir key4hep/first_trial
       - bash
       - source /cvmfs/sw.hsf.org/key4hep/setup.sh 
       - git clone https://github.com/iLCSoft/MarlinKinfitProcessors.git
       - mkdir run, cp WW5CFit.xml there, change input file to   /pnfs/desy.de/ilc/prod/ilc/mc-opt-3/ild/dst-merged/500-TDR_ws/4f_WW_hadronic/ILD_l5_o1_v02/v02-00-01/rv02-00-01.sv02-00-01.mILD_l5_o1_v02.E500-TDR_ws.I250006.P4f_ww_h.eL.pR.n001.d_dstm_10398_0.slcio
  - here we are - now back to CLIC reconstruction instructions... https://key4hep.github.io/key4hep-doc/k4marlinwrapper/doc/starterkit/k4MarlinWrapperCLIC/Readme.html#reconstruction
     - need appropriate DD4hepXMLFile.... check what's in $LCGEO/ILD/compact/ILD_l5_o1_v02 - many xml files ....let's take simply *ILD_l5_o1_v02.xml* and hope for the best...
     - first try Marlin directly:
        - Marlin WW5CFit.xml --InitDD4hep.DD4hepXMLFile=$LCGEO/ILD/compact/ILD_l5_o1_v02/ILD_l5_o1_v02.xml
        - loading libraries works... but awfully slow ... and crash.
        -  ok, my mistake: WW5CFit.xml is so old that it doesn't have DD4HEPInit yet... Adding it from MarlinKinfitTutorials/ZHAnalysis.html, and try again
        -  works, and library loading fast :) 
        -  **TODO: find out where to get GEAR file in key4hep world**, currently still using /cvmfs/ilc.desy.de/sw/ILDConfig/v02-02-03/StandardConfig/production/Gear/gear_ILD_l5_v02.xml
        -  and hey, I get a root file out !!! :)


## November 11, 2022

-  login in to NAF again, bash, source /cvmfs/sw.hsf.org/key4hep/setup.sh 
-  now try MarlinWrapper as in https://key4hep.github.io/key4hep-doc/k4marlinwrapper/doc/starterkit/k4MarlinWrapperCLIC/Readme.html#reconstruction-with-gaudi-through-k4marlinwrapper
   -  convertMarlinSteeringToGaudi.py WW5CFit.xml WW5CFit.py  
   -  results in Exception when getting trees: ParseError('XML declaration not well-formed: line 1, column 20')
   -  arggh. 
   -  OK, git clone https://github.com/iLCSoft/CLICPerformance to check for difference wrt CLIC steering file
   -  ah, found typo (encondin instead of encoding), fixed, now have a WW5CFit.py :)
   -  changes proposed to .py file in instruction seem to be fine already...try it
   -  k4run WW5CFit.py
   -  runs, and produces a root file -> check for differences...=> only very few events => ah, MaxRecordNumber is not translated in xml->py conversion
   -  set from 10 to 1000 and try again... hm, it says it reads 1000 event and finds 4000 jets
   -  ... but actually histos still nearly empty... log file seems to indicate that histograms are re-creted for each event ?!
      **=> TODO: ask André whether RAIDA works at all with MarlinWrapper and/or what alternative way there is to fill histograms**




