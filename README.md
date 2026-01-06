# JSChemify

A "pretty okay" lightweight, self-contained cheminformatics library written in native JavaScript. JSChemify provides essential chemical structure manipulation, analysis, and visualization capabilities directly in the browser without requiring external dependencies.

## Features

- **Chemical I/O**: SMILES and Molfile import/export
- **Visualization**: PNG/SVG rendering with customizable styling
- **2D Coordinates**: Automatic coordinate generation for chemical structures
- **Descriptors**: E-state vectors and Kier-Hall molecular descriptors
- **Data Analysis**: Built-in spreadsheet with custom calculated columns
- **Machine Learning**: Basic linear regression for QSAR modeling
- **Structure Manipulation**: Add/remove atoms and bonds programmatically
- **Chemical Properties**: Molecular weight, formula, and other basic properties
- **Ring Analysis**: Ring detection and ring system network analysis
- **Export Options**: Excel export with structure hover captions
- **Standards**: InChI and InChIKey generation (with WASM library)
- **Lightweight**: ~350KB uncompressed, <50KB when minimized and compressed

## Basic Live Demos

 - [Path Notation Example](https://tylerperyea.github.io/JSChemify/path.html)
 - [Spreadsheet](https://tylerperyea.github.io/JSChemify/spreadsheet.html)


## Quick Start

### Basic Usage

```javascript
// Parse a SMILES string
const molecule = JSChemify.Chemical("CCO"); // Ethanol

// Generate 2D coordinates and render as SVG
molecule.generateCoordinates();
const svg = molecule.getSVG();
document.getElementById("structure").innerHTML = svg;

// Get basic properties
console.log("Molecular Weight:", molecule.getMolWeight()); // 46.07
console.log("Molecular Formula:", molecule.getMolFormula()); // C2H6O
```

### Working with Molfiles

```javascript
// Parse a molfile
const molfileText = `...`; // Your molfile content
const molecule = JSChemify.Chemical(molfileText);

// Convert to SMILES
const smiles = molecule.toSmiles();
console.log("SMILES:", smiles);

// Export back to molfile
const newMolfile = molecule.toMol();
```

### Structure Manipulation

```javascript
// Create a molecule from scratch
const mol = JSChemify.Chemical();

// Add atoms
const carbon1 = mol.addNewAtom("C");
const carbon2 = mol.addNewAtom("C");
const oxygen = mol.addNewAtom("O");

// Add bonds (atom indices, bond order)
mol.addNewBond(0, 1, 1); // Single bond between C1-C2
mol.addNewBond(1, 2, 1); // Single bond between C2-O

// Generate coordinates and render
mol.generateCoordinates();
console.log("Generated SMILES:", mol.toSmiles()); // "CCO"
```

### Molecular Descriptors

```javascript
const molecule = JSChemify.Chemical("c1ccccc1"); // Benzene

// Calculate E-state vector
const estateVector = molecule.getEStateVector();
console.log("E-state descriptors:", estateVector.serialize());

// Get connectivity indices
const chi0 = molecule.getConnectivityIndexS(0);
const chi1 = molecule.getConnectivityIndexS(1);
console.log("Chi0:", chi0, "Chi1:", chi1);
```

### Data Analysis with Collections

```javascript
// Create a collection from tab-delimited data
const data = `SMILES    Name    Activity
CCO    Ethanol    1.2
CCC    Propane    0.8
CCCC    Butane    1.5`;

const collection = JSChemify.ChemicalCollection()
    .fromSmilesFile(data);

// Add calculated properties
collection.computeNewProperty("MolWeight", (chem) => {
    return chem.getMolWeight();
});

// Display as interactive table
document.getElementById("table").innerHTML = collection.$getTableHTML();
```

### Linear Regression Example

```javascript
// Prepare data for QSAR modeling
const molecules = ["CCO", "CCC", "CCCC", "CCCCC"];
const activities = [1.2, 0.8, 1.5, 2.1];

// Calculate descriptors
const descriptors = molecules.map(smi => {
    const mol = JSChemify.Chemical(smi);
    return [mol.getMolWeight(), mol.getConnectivityIndexS(0)];
});

// Build linear regression model
const regression = JSChemify.LinearRegression()
    .setX(descriptors)
    .setY(activities.map(a => [a]));

const coefficients = regression.calculate();
const rSquared = regression.getRSquared();

console.log("Model coefficients:", coefficients);
console.log("R²:", rSquared);
```

### Advanced Rendering with Highlighting

```javascript
const molecule = JSChemify.Chemical("CCN(CC)CC"); // Triethylamine

// Add atom contributions for highlighting
molecule.computeContributions((mol) => mol.getMolWeight());

// Generate coordinates and render with highlights
molecule.generateCoordinates();
const svg = molecule.getSVG();
document.getElementById("highlighted").innerHTML = svg;
```

### Ring Analysis

```javascript
const molecule = JSChemify.Chemical("C1CCCCC1C2CCCCC2"); // Cyclohexyl-cyclohexane

// Detect rings
const rings = molecule.getRings();
console.log("Number of rings:", rings.length);

// Get ring systems
const ringSystems = molecule.getRingSystems();
console.log("Ring systems:", ringSystems.length);

// Check if specific atoms are in rings
molecule.getAtoms().forEach((atom, i) => {
    console.log(`Atom ${i} in ring:`, atom.isInRing());
});
```

## File Format Support

### Input Formats
- SMILES strings
- MDL Molfiles (V2000 and V3000)
- SDF files (multiple structures)
- Tab-delimited text files

### Output Formats
- SMILES strings
- MDL Molfiles
- SDF files
- SVG graphics
- PNG images (via canvas)
- Excel files with embedded structures

## Browser Integration

JSChemify works entirely in the browser and can be easily integrated into web applications:

```html
<!DOCTYPE html>
<html>
<head>
    <script src="jschemify.js"></script>
</head>
<body>
    <div id="molecule-display"></div>
    <script>
        const mol = JSChemify.Chemical("CCO");
        mol.generateCoordinates();
        document.getElementById("molecule-display").innerHTML = mol.getSVG();
    </script>
</body>
</html>
```

## Examples

The library includes several complete examples:

- **index.html**: Basic similarity searching with E-state vectors
- **path.html**: Path notation for compact coordinate storage
- **spreadsheet.html**: Interactive chemical data analysis

## License

The Woody Guthrie License

This code belongs to everybody. Anyone caught using, copying, modifying, or redistributing this software will be a mighty fine friend of ours. Use it, hack it, share it, sell it, teach it. We wrote it and that's all we wanted to do.

This software is released into the public domain. No warranty provided - if it breaks, you get to keep both pieces. But hey, that's what makes it fun!

(Technically this is an MIT license though, if you're a nerd about such things).

## Contributing

This is a research/educational library. While functional, it may contain bugs or limitations. Contributions and feedback are welcome!
