# swc_specification
Information about the SWC file specification

## Specification of standard SWC - adpated from (3)

An SWC file (S.W.C. encodes for the last names of its initial designers Ed Stockley, Howard Wheal, and Robert Cannon) is a text file that starts with a header section in which each line starts with the symbol #. Some SWC-variants use this section to store information about the data in an orderly fashion, others treat is as a free-text field. According to the original publication (4) that introduced SWC, the header contains the following prescribed fields:

Field | Description 
--- | --- 
ORIGINAL_SOURCE | File type delivered by digitisation equipment 
CREATURE | Species from which the cell came
REGION | Brain region
FIELD/LAYER | Location within region 
TYPE| Cell type 
CONTRIBUTOR | Name, initials, organisation, e.g. Turner DA, Duke
REFERENCE | Where the data has been published
RAW | File name of original data
EXTRAS | Files containing further information on this cell
SOMA_AREA | Area of some (in mm2)
SHRINKAGE_CORRECTION | x, y and z correction factors
VERSION_NUMBER | To identify different versions of the same raw data
VERSION_DATE | Date this version was created (yyyy-mm-dd)
SCALE | Used internally to record applied shrinkage corrections

However, these fields are not present in the NeuroMorpho.org SWC format and therefore cannot be considered a required component of SWC.

Below the header section, a points matrix with 7 columns follows. It contains the points traced along the neuronal tree. The seven numbers in each row are separated by spaces, and have the following meaning:

Column index | Field | Description
--- | --- | ---
1 | SampleID | Sample identifier. A positive integer.
2 | TypeID | Type identifier
3 | x | X-position in micrometers
4 | y | Y-position in micrometers
5 | z | Z-position in micrometers
6 | r | Radius in micrometers (half the cylinder thickness)
7 | ParentID | Parent sample identifier.

The basic set of types used in NeuroMorpho.org SWC files are: 
TypeID | Description 
--- | --- 
-1 | root
 0  | undefined
 1  | soma
 2  | axon
 3  | (basal) dendrite
 4  | apical dendrite
 5+ | custom
 
In addition, some SWC-variants use the following types 5 and 6:
TypeID | Description 
--- | --- 
5 | branch point (redundant: branch is a point with multiple children)
6 | end point (redundant: end point is a point with zero children)

Parent defines how points are connected to each other. In a tree, multiple points can have the same ParentID. The first point in the file must have a ParentID equal to -1, which represents the root point. Parent samples must be defined before they are being referred to. By counting how many points refer to the a given parent, the number of its children can be computed.

## SWC Example - adapted from Ascoli et al, 2001

![image](https://user-images.githubusercontent.com/8552673/157725540-a49e8ab7-e930-401c-938b-713dd093dc2c.png)

This extremely simplified neuronal structure was obtained by extensive pruning of a dentate gyrus granule cell in (4). The left panel shows the digital SWC representation:
every cylindrical compartment is described by a row containing:
- A label (ID or SampleID, the same numbers reported next to the branches in the right panel
- A tag (T or TypeID, 1 for soma, 3 for dendrite),
- Cartesion positions (x,y,z in micrometers), 
- Radius (r) and 
- Connectivity (C or ParentID, representing the label of parent, -1 indicates no parent)

## Syntax of basic SWC reconstruction in EBNF - adapted from Nanda et al, 2018

newline = "\n" ; 

letter = "A" | "B" | "C" | "D" | "E" | "F" | "G" 
| "H" | "I" | "J" | "K" | "L" | "M" | "N" 
| "O" | "P" | "Q" | "R" | "S" | "T" | "U" 
| "V" | "W" | "X" | "Y" | "Z" | "a" | "b" 
| "c" | "d" | "e" | "f" | "g" | "h" | "i" 
| "j" | "k" | "l" | "m" | "n" | "o" | "p" 
| "q" | "r" | "s" | "t" | "u" | "v" | "w" 
| "x" | "y" | "z" ; 

digit = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9" ; 

symbol = "[" | "]" | "{" | "}" | "(" | ")" | "<" | ">" 
| "'" | '"' | "=" | "|" | "." | "," | ";" ; 

integer = [+|-] digit{digit} ; 

double = integer ["."] {integer} ; 

character = letter | double | symbol | "_" | " "; 

header = "#" character {character} newline ["#" character {character} newline] ; 

Index = integer ; 

Type = integer ; 

X = double ; 

Y = double ; 

Z = double ; 

Radius = double ; 

Parent = integer ; 

node = Index " " Type " " X " " Y " " Z " " Radius " " Parent ; 

neuron_tree = node newline {node newline} ; 

SWC = [header] neuron_tree ;


## References
1. Ascoli GA, Krichmar JL, Nasuto SJ, Senft SL. Generation, description and storage of dendritic morphology data. Philos Trans R Soc Lond B Biol Sci. 2001;356(1412):1131-1145. doi:10.1098/rstb.2001.0905 ([PMCID: PMC1088507](https://www.ncbi.nlm.nih.gov/sites/ppmc/articles/PMC1088507/))
2. Nanda S, Chen H, Das R, et al. Design and implementation of multi-signal and time-varying neural reconstructions. Sci Data. 2018;5:170207. Published 2018 Jan 23. doi:10.1038/sdata.2017.207 ([PMCID: PMC5779069](https://www.ncbi.nlm.nih.gov/sites/ppmc/articles/PMC5779069/))
3. https://neuroinformatics.nl/swcPlus/ "SWC plus (SWC+) format specification"
4. Cannon RC, Turner DA, Pyapali GK, Wheal HV. An on-line archive of reconstructed hippocampal neurons. Journal of Neuroscience Methods. 1998 Oct;84(1-2):49-54. DOI: 10.1016/s0165-0270(98)00091-0. [PMID: 9821633](https://europepmc.org/article/MED/9821633).
5. https://en.wikipedia.org/wiki/Extended_Backus%E2%80%93Naur_form
