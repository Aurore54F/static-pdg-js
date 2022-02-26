# static-pdg-js:
## Static JavaScript Analysis: AST, Control Flow, Data Flow, and Pointer Analysis


## Summary
We propose a tool to statically analyze JavaScript code.
To this end, we build the AST (Abstract Syntax Tree) of an input JavaScript file.
To reason about the conditions that have to be met for a specific execution path to be taken, we subsequently add control flow edges to the AST. We refer to the resulting graph as the CFG (Control Flow Graph).
Next, to reason about variable dependencies, we add data flow edges to the CFG.
Finally, to compute variable values, we perform a pointer analysis.
We refer to the resulting graph as the PDG (Program Dependence Graph).

Please, note that in its current state, the code is a PoC and not a fully-fledged production-ready API.


### CFG and PDG Definitions
We adopt a definition of the CFG that slightly differs from Allen’s as we enhance our AST with control flow edges. This way, we build a joint structure that combines control flow information with the fine-grained AST nodes and edges.
Our PDG also slightly differs from the definition of Ferrante et al. as we chose to add data flow edges to our CFG. This way, we retain information regarding statement order and have a fine-grained representation of the data flows directly at the variable level (as we build the CFG upon the AST).  
Additional details can be found in [my dissertation](https://publications.cispa.saarland/3471/7/fass2020thesis.pdf).

### Papers based on this Tool

This code has been used to statically analyze browser extensions. See DoubleX [paper](https://swag.cispa.saarland/papers/fass2021doublex.pdf) & [code](https://github.com/Aurore54F/DoubleX).  
Preliminary versions of this code were also used to detect malicious JavaScript samples: HideNoSeek [paper](https://swag.cispa.saarland/papers/fass2019hidenoseek.pdf) & [code](https://github.com/Aurore54F/HideNoSeek) and JStap [paper](https://swag.cispa.saarland/papers/fass2019jstap.pdf) & [code](https://github.com/Aurore54F/JStap).  
And to study JavaScript code transformation techniques: [paper](https://swag.cispa.saarland/papers/moog2021statically.pdf) & [code](https://github.com/MarM15/js-transformations).


## Setup

```
install python3 # (tested with 3.7.3 and 3.7.4)

install nodejs
install npm
cd src
npm install esprima # (tested with 4.0.1)
npm install escodegen # (tested with 1.14.2 and 2.0.0)
```

To install graphviz (only for drawing graphs, not yet documented, please open an issue if interested)
```
pip3 install graphviz
On MacOS: install brew and then brew install graphviz
On Linux: sudo apt-get install graphviz
```

## Usage

### Single PDG Generation

To generate the PDG of a specific *.js file, launch the following python3 commands from the `src` folder location:
```
>>> from build_pdg import get_data_flow
>>> pdg = get_data_flow('INPUT_FILE', benchmarks=dict())
```

Per default, the corresponding PDG will not be stored. To store it in an **existing** PDG_PATH folder, call:
```
$ python3 -c "from build_pdg import get_data_flow; get_data_flow('INPUT_FILE', benchmarks=dict(), store_pdgs='PDG_PATH')"
```

Note that we added a timeout of 10 min for the data flow/pointer analysis (cf. line 149 of `src/build_pdg.py`), and a memory limit of 20GB (cf. line 115 of `src/build_pdg.py`).

### PDG Generation - Multiprocessing

Let's consider a directory `DIR` containing several JavaScript files to analyze. To generate the PDGs (= ASTs enhanced with control and data flow, and pointer analysis) of all these files, launch the following shell command from the `src` folder location:
```
$ python3 -c "from build_pdg import store_pdg_folder; store_pdg_folder('DIR')"
```

The corresponding PDGs will be stored in `DIR/PDG`.

Currently, we are using 1 CPU, but you can change that by modifying the variable NUM\_WORKERS from `src/utility_df.py` (the one **line 51**).


## License

This project is licensed under the terms of the AGPL3 license, which you can find in ```LICENSE```.