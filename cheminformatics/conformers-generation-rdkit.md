# Conformers Generation with RDKit

```python
mol = Chem.MolFromSmiles(smi)

mol = Chem.AddHs(mol)

params = AllChem.ETKDGv2() # Riniker & Landrum, 2015
params.randomSeed = 42

AllChem.EmbedMultipleConfs(mol, numConfs=1, params=params)
```

## Imports

```python
from rdkit import Chem
from rdkit.Chem import AllChem
```
