# Key4HEP_first_experience

This is a diary of my first attempt to use Key4HEP.

# November 10, 2022
  - "google" (actually using ecosia) key4hep, first hit yields https://key4hep.github.io/key4hep-doc/
  - log in to lxplus.cern.ch
  - create new directory key4hep
  - start to follow instructions on https://key4hep.github.io/key4hep-doc/ :
     - source /cvmfs/sw.hsf.org/key4hep/setup.sh 
         - yields: /cvmfs/sw.hsf.org/key4hep/setup.sh:export:4: not valid in this context: key4hep-stack/2022-10-30
         - probably need to use bash ? => yes, when using bash, the setup scripts seems to work
     - since I don't want to run the CLIC simulation & reconstruction, but a kinematic fit example, I clone 
       - the MarlinKinfit tutorial:  git clone https://github.com/ILDAnaSoft/MarlinKinfitTutorial.git
       - MarlinKinfitProcessors : git clone https://github.com/ILDAnaSoft/MarlinKinfitTutorial.git

