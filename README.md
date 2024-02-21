# PodPals - Friends of the ORCA computational chemistry suite (v1.0)
![PodPal cover image - generated by Dall-E](https://github.com/HopkinsLaboratory/ORCA_Analysis_GUI/blob/master/GUI/gui/ORCAs_pod_helper_CoverImage.png)

## Introduction
This repository contains code for the **P**ythonic **O**rca **D**ata **P**rocessing and **A**nalysis **L**ibraries (PodPals), a Graphical User Interface (GUI) designed to streamline the creation and analysis of files generated by the [ORCA computational chemistry package](https://orcaforum.kofo.mpg.de/app.php/portal). If you have anny suggestions for additions to the PodPals GUI, please email me at [cieritano@uwaterloo.ca](mailto:cieritano@uwaterloo.ca).

## Installation and prerequisites
The GUI can be launched immediately via [Launcher.py](/GUI/Launcher.py), which will prompt the user to install any missing packages and automatically add them to PATH (as required). However, it's advisable to pre-install the required software and Python modules before you begin using the GUI.

### External software
Ensure the following software is installed on your system:

- **Python 3.11+**: Available from [Python Downloads](https://www.python.org/downloads/). During installation, add Python to your system's PATH and choose a convenient installation location, such as `C:\Python311`.
- **GitHub Desktop**: Available from [GitHub Desktop](https://desktop.github.com/). Installation does not require a GitHub account.
- **Git**: Available from [Git SCM](https://git-scm.com/). Install using the default settings in the suggested location, unless you have specific preferences. No GitHub account is required.

### Python modules
Install the necessary Python modules using `pip` in your command prompt:

```console
pip install importlib numpy scipy matplotlib git lxml PyQt6 pyarrow pandas openpyxl csv natsort
```

## PodPals code
To clone the PodPals repository, open your terminal or command prompt and enter the following command: 

```console
git clone https://github.com/HopkinsLaboratory/PodPals.git /path/to/your/desired/directory
```

This will create a local copy of the repository on your machine in a directory of your choice; replace `/path/to/your/desired/directory` with the actual path where you want the repository to be cloned. If the specified path doesn't exist, Git will create the necessary directories for you.

If you prefer not to use the command line, you can download the repository directly from the GitHub website. Simply click on the green "Code" button at the top right of the webpage, and then select "Download ZIP" from the dropdown menu to download the repository as a ZIP file.

Launch the GUI by running [Launcher.py](/GUI/Launcher.py) in your preferred Python environment. If there are any missing dependencies or prerequisites, you will be prompted to install them.

# GUI functions and usage
This GUI features 13 modules/tabs to facilitate workflows that use the ORCA computational suite, each of which has integrated error handling. Any errors or issues encountered will be reported to the `Status window`, prompting the user to adjust their input accordingly. Please report any unhandled/unclear errors or suggestions for additions to the code via the [Issues](https://github.com/HopkinsLaboratory/ORCA_Analysis_GUI/issues) section.

## T1. CREST .xyz Splitter:
This module extracts conformers and their energies identified by the [Conformer Rotamer Ensemble Sampling Tool (CREST)](https://crest-lab.github.io/crest-docs/), an exceptionally efficient program for mapping low-energy conformations of any analyte. *XYZ* coordinates of each conformer are stored in the `crest_conformers.xyz` file, which is generated upon completion of a CREST run. The `CREST .xyz splitter` module extracts each conformer to individual `.gjf`, `.xyz`, or `.inp` files . Extracting of conformers to alternative file types will be added in future updates. 

### **Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T1_CREST_xyz_splitter)**: 
1. **Select .xyz File**: 
    - Click the `Browse` button to open a file explorer window. Navigate to and select the `crest_conformers.xyz` file you wish to split, or paste the file path into the dialog box. 
2. **Specify Output Basename**: 
    - Enter a basename for the output files in the designated input box. Extracted geometries will be saved to files called `basename_N`, where N is a unique integer for conformer distinction.
3. **Export file type**: 
    - Choose whether to extract conformers from `crest_conformers.xyz` to `.gjf`, `.xyz`, or `.inp` files. 
4. **Execute Splitting**: 
    - Click the `Run` button to start the splitting process, which extracts all conformers from `crest_conformers.xyz` to individual `.gjf`, `.xyz`, or `.inp` files that are saved to a new directory called `Conformers`.
    - `Energies.csv`, a file summarizing the name, energy, and relative energy of each extracted conformer (sorted in ascending order), is also created and saved to `Conformers`. This file is directly compatible with [T2. Cosine similarity sorting](#T2-Cosine-similiarity-sorting)

## T2. Cosine similarity sorting:
This module processes multiple .gjf, .inp, and/or .xyz files to identify unique conformations using the cosine similarity method detailed [here](https://www.frontiersin.org/articles/10.3389/fchem.2019.00519/full). It is specifically designed to integrate with outputs from the [CREST .xyz Splitter](#T1-crest-xyz-splitter), including the `Energies.csv` file.

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T2_Cosine_sim):
1. **Directory or .csv input:**
    - Click the `Browse dir` button to select a directory containing your .gjf, .xyz, and/or .inp files, or paste the directory path into the dialog box.
    - Alternatively, use the `Browse .csv` button to select a .csv file, or paste the directory path into the dialog box. Ensure your .csv file is formatted correctly, with headers that are `Filename`, `Energy / hartree`, and `Relative Energy / kJ mol**-1`.
        - While the `Energies.csv` file generated by the CREST .xyz Splitter is already in the correct format, users may also supply their own .csv file, provided it adheres to the specified format: 
            - The .csv's first row must include these headers: `Filename` `Energy / hartree` and `Relative Energy / kJ mol**-1`. 
            - **Filename column**: Entries here must list the filename with its extension. The files mentioned must be located in the same directory as the Energies.csv file.
            - **Energy columns**:  Entries must be numeric and arranged in ascending energy order. An error will be displayed if the files are not sorted correctly or if the entries are not numeric.
            - The .csv file must include *at least* two rows beyond the header. Fewer entries imply that only a single file is present, precluding the possibility of any pairwise comparison.

2. **Setting the similiarity threshold:**
    - Adjust the `Cosine similarity threshold (*100):` field to set your desired threshold for similarity comparisons. Thresholds can be defined anywhere between 0.00 to 99.99.
    - The similiarity between two geometries is defined by the cosine of the angle between the two vectors $(\vec{V}_a, \vec{V}_b)$, where each vector is a 1D-array of the mass-weighted distance ($d_{CoM}$) of each atom *i* from the molecule's centre of mass ($CoM$):

    $$x_{CoM} = \frac{\sum_{i} x_i m_{i}}{\sum_{i} m_{i}}, \quad y_{CoM} = \frac{\sum_{i} y_i m_{i}}{\sum_{i} m_{i}}, \quad z_{CoM} = \frac{\sum_{i} z_i m_{i}}{\sum_{i} m_{i}}$$

    $$d_{CoM} = m_{i} \cdot \sqrt{(x_{i} - x_{CoM})^2 + (y_{i} - y_{CoM})^2 + (z_{i} - z_{CoM})^2}$$

    - The 1D array is sorted from smallest to largest so that variations in atom ordering from the input file and rotational equivalences (e.g., the C2 symmetry of the phenyl moiety) do not result in unique geometries. The cosine similarity between $(\vec{V}_a, \vec{V}_b)$ is then evaluated by:

    $$\text{sim}(\vec{V}_a, \vec{V}_b) = \left(1 - \left(\frac{1}{\pi} \right) \cdot \arccos\left(\frac{\vec{V}_a \cdot \vec{V}_b}{\|\vec{V}_a\| \cdot \|\vec{V}_b\|}\right)\right) \cdot 100$$

3. **Writing Pairwise Similarities:**
    - Check the `Write pairwise similarities to a .csv file?` option if you wish to save the results of the pairwise similarity comparisons to a .csv file. If checked, $sim(\vec{V}_a, \vec{V}_b)$ will be written to a .csv for each pairwise combination of molecules provided in the directory.

4. **Running the Analysis:**
    - Once you have configured your inputs and options, click the `Sort files by cosine similarity` button to initiate the analysis.
    - The module performs pairwise comparisons sequentially, beginning with the vector assoicated with the first file in the directory or .csv ($\vec{V}_a$)and proceeding to the last ($\vec{V}_b$).
        - A pairwise $sim(\vec{V}_a, \vec{V}_b)$ below the defined threshold identifies a unique geometry. Conversely, a similarity at or above the threshold indicates a duplicate geometry. Molecules identified as duplicates are excluded from any subsequent pairwise comparisons.
    - The comparison process then continues by calculating similarities between the first identified unique geometry (the first unique $\vec{V}_b$ identified when comparising against $\vec{V}_a$) and all other unique geometries, excluding the initial file ($\vec{V}_a$). This iterative approach repeats, exluding all duplicates and the initial vector $\vec{V}_a$ until no additional pairwise comparisons can be performed. This iterative process is shown below:
    
        ![Cosine similiarity sorting workflow](https://github.com/HopkinsLaboratory/ORCA_Analysis_GUI/blob/master/Sample_Files/T2_Cosine_sim/Cosine_sim_sorting_workflow.png)
    - Unique isomers are saved to a newly created directory named `uniques_sim_N`, where N represents the similarity threshold.
    - If the `Write pairwise similiarities to a .csv file` option was checked, `pairwise_similiarities.csv` will be written to the `uniques_sim_N` directory. 

## T3. Generate ORCA .inp: 
This module processes a directory containing .gjf, .inp, and/or .xyz files and converts each into a new ORCA .inp file with its content controlled through the GUI interface. 

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T3_Generate_ORCA_inp): 
1. **Select directory**:
    - Use the `Browse` button to choose the directory containing at lease one .gjf, .xyz, and/or .inp file, or paste the path directly into the dialog box.
2. **Configure the performance settings**: 
    - Adjust the memory per core (in MB), number of cores, charge, and multiplicity as required for your calculations.
3. **Choose the calculation method**: 
    - Select a predefined method (DFT or CCSDT) or customize your own. The method line can be edited in the Custom mode for specific requirements.
4. **Compute ESP charges (optional)**: 
    - Optionally compute ESP charges by selecting the `Compute ESP Charges?` checkbox. This action enables additional input fields for grid and Rmax (both in Angstroms) and writes the following block to the .inp file:
        ```bash
        %chelpg
        grid 0.1
        rmax 3.0
        end
        ```
    - Reccomended values for $grid$ and $R_{max}$ are 0.1 Å and 3.0 Å, respectively. With these settings, the CHELPG partition scheme uses a grid composed of points spaced apart by 0.1 Å, where each $grid$ point is at most 3.0 Å away from any atom in the molecule. For larger molecules (> 350 electrons without pseudopotentials), using a coarser grid with the parameters $grid = 0.3 Å$ and $R_{max} = 3.0 Å$ is reccomended.
5. **Additional options**: 
    - Calculate the Hessian on the first optimization step. This writes the following block to the .inp file:
        ```bash
        %geom
        Calc_Hess true
        end
        ```
    - Compute dipole/quadrupole moments and isotropic polarizability. Selecting this option writes the following block to the .inp file:
        ```bash
        %elprop
        Dipole true
        Quadrupole true
        Polar 1
        end
        ```
    - Call XYZ coordinates from an external .xyz file. Selecting this option directly references an external .xyz file where the *XYZ* coordinate block would normally be in the .inp file. For example:
        ```bash
        *xyzfile 1 1 Fluoxetine_1.xyz
        ```
6. **Generate .inp files**: 
    - Click the `Run` button to create ORCA .inp files for each valid input file within the specified directory.
    - .inp files are saved to a new directory called `New_Inputs` or `New_Inputs_xyz` depending on whether the `Call XYZ coordinates from external .xyz file?` option was selected. 

## T4. ORCA .out to ORCA .inp (Opt/Freq): 
This module processes a directory containing .out files from ORCA calculations, extracting the final geometries to generate new ORCA .inp files for optimization/frequency jobs. The configuration of these .inp files, including their content and options, is managed through the GUI interface.

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T4_ORCA_out_to_ORCA_inp):
The usage of this module aligns with the methods described in [T3. Generate ORCA .inp](#t3-generate-orca-inp), with one additional feature:
- **Additional Options**: There's an option to `Write final coordinates to a .gjf file`. Selecting this checkbox creates `.gjf` files alongside `.inp` files, facilitating visualization using external software packages like GaussView.

## T5. ORCA .out to ORCA .inp (VGFC/TD-DFT):
This module processes a directory containing .out files from ORCA calculations, extracting the final geometries to generate new ORCA .inp files for computing UV-Vis absorption spectra using the Vertical Gradient Franck-Condon (VG|FC) method, also known as the Independent Mode Displaced Harmonic Oscillator (IMDHO) model. The configuration of these .inp files is customizable via the GUI interface.

### Usage, and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T5_ORCA_out_to_ORCA_VGFC):
The usage of this module aligns with the methods described in [T3. Generate ORCA .inp](#t3-generate-orca-inp), with additional options specific to VG|FC calculations:
- **Directory**: Provide a path to a directory with ORCA .out files from a `Freq` job, ensuring both the `.out file` and its corresponding `.hess file` (generated upon completing the Freq job) share the same basename and are located in the same directory. If a .hess file is missing, an error will be reported, and no .inp file will be created file pair.
- **Performance Settings**: These remain unchanged from T3, though it is recommended to allocate additional memory per core for molecules to avoid out-of-memory errors (*ca.* 5000 MB for systems with 150+ electrons) 
- **Calculation Method**: While a default method is preset, customization is available. The method must include the `ESD(ABS)` keyword to initiate a VG|FC calculation. Future updates may expand support based on user feedback.
- **# states**: Define the number of excited states to be calculated, ranging from the ground state ($S_0$) to the N-th excited state ($S_N$): $S_0 \rightarrow S_N$
- **Additional Options**: There's an option to `Write final coordinates to a .gjf file`. Selecting this checkbox creates `.gjf` files alongside `.inp` files, facilitating visualization using external software packages like GaussView.

## T6. Plot optimization trajectory:
This module visualizes the optimization trajectory from an ORCA calculation, plotting energy changes, SCF energy, maximum gradient, maximum step, RMS gradient, and RMS step as functions of the number of optimization cycles. It is designed for ORCA .out file content generated as a result of performing geometry optimizations via the `opt` keyword (or any of its variants).

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T6_ORCAplt_OptTrajectory): 
1. **Select an ORCA .out file**:
    - Click the `Browse` button to navigate and select the desired .out file for analysis, or paste the directory path with the .out filename directly into the dialog box.
2. Save the plot (optional):
    - To save the generated plot, check the `Save the plot to an external file?` option.
3. Generate the Plot:
    - Click the `Plot` button to visualize the optimization trajectory based on the selected .out file. The plot contains the following information:
        - **Energy Change and SCF Energy**: Visualizes the overall energy progression and stability of the SCF calculations. The energy change should go to zero as the calculation progresses.
        - **Max gradient and step**: Highlights the largest gradient and step taken during optimization, indicating convergence towards a minimum.
        - **RMS gradient and step**: Provides a measure of the overall movement across all atoms.
        - If the .out file contains a single optimization step, only the **Energy Change** and **SCF Energy** plots will be generated. 

##  T7. Calculate thermochemistry: 
This module computes thermochemical corrections using the methodology described by [McQuarrie and Simon (1999)](https://gaussian.com/wp-content/uploads/dl/thermo.pdf) on each .out file in a directory that was generated from an inputs containing the `Freq` keyword (e.g., from an Opt Freq job). Users can specify temperature and pressure in which to evalaute thermochemistry, and whether to compute thermochemistry using scaled vibrational frequencies. Additionally, the module notifies users of incomplete Opt/Freq jobs and the presence of imaginary frequencies.

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T7_ORCAThermochemAnalyzer): 
1. **Select directory**: 
    - Use the `Browse` button to choose the directory that contains at least one .out file, or paste the directory path directly into the dialog box.
2. **Configure temperature and pressure**: 
    - Define the desired temperature (in Kelvin) and pressure (in Pascal) to evalaute thermochemical quantities at.
3. **Apply the vibrational scaling factor**:
    - Enter a multipliactive scaling factor to adjust all vibrational frequencies for anharmonicity. If no scaling is desired, leave at the default value of `1.00`:
        $$v_{\text{scaled}} = (\text{scaling factor}) \cdot v_{\text{orig}}$$
4. **Choose a sorting option**: 
    - Choose sorting criteria for output Excel columns, including Filename, Gibbs Energy (G), Enthalpy (H), Entropy (S), Thermal Energy (E), and Zero-point Energy (ZPE).
5. **Calculate thermochemistry**:
    - Click the `Run` button to begin the calculation process. Thermochemistry will be calculated for each .out file found within the provided directory and written to a .csv. 
    - The .csv file, named `Thermo_data_temp_pressure_vibscl.csv` (with temp, pressure, and vibscl representing the specified temperature, pressure, and vibrational scaling factor, respectively), includes:
        - Filename
        - Number of imaginary freqs
        - Electronic energy
        - Thermal energy
        - Total enthalpy (H)
        - Total entropy (T * S)
        - Gibbs energy (G) 
        - A, B, and C rotational constants
        - Rotational symmetry number
        - Dipole components (*X*, *Y*, *Z*) and total dipole moment
        - Isotropic polarizability
        - Partition functions (translational, rotational, vibrational, and electronic)

## T8. Extract coupled cluster energies: 
This module extracts coupled cluster energies from each .out file in a directory that was generated from an input requesting a coupled cluster calculation (e.g.,`! CCSD(T) cc-pVTZ`, `! CCSD cc-pVTZ`, and `! DLPNO-CCSD(T) cc-pVTZ cc-pVTZ/C`). Additional quantities are extracted to aid the user in evaluating the accuracy and reliability of their calculation.

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T8_ORCA_CCSDT_Analyzer): 
1. **Select directory**: 
    - Use the `Browse` button to choose the directory containing your ORCA .out files, or paste the file path directly into the dialog box.
2. **Extract Coupled Cluster Energies**:
    - Press the `Extract coupled cluster energies` button to begin the extraction process. Coupled cluster energies will be extracted from each .out file found within the provided directory and written to a .csv. 
    - The .csv file, named `Coupled_cluster_energies.csv`, includes:
        - Filename
        - CCSD(T) energy (if requested)
        - CCSD energy
        - Triples correction (if requested)
        - Final correlation energy
        - T1 diagnostic (a measure to evaluate the reliability of coupled cluster results). Values > 0.02 are unreliable. 

## T9. Extract/plot IR spectra: 
This module extracts vibrational frequency data from ORCA .out files to generate IR spectra. IR spectra are generating by applying a Gaussian convolution to each vibrational frequency, with the height of hte Gaussian calculated by ORCA from the change in molecule's dipole moment as it undergoes the specific vibration, and a Full Width at Half Maximum (FWHM) that can be customized by the user. The resulting spectra are exported to an Excel file on a user-specified range and step size in cm<sup>-1</sup>. Additionally, the module offers the option of scaling vibrational frequencies, normalizing spectra, and plotting to an external file.

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T9_ORCA_Vib_Spectrum_Analyzer): 
1. **Select directory**: 
    - Use the `Browse` button to choose the directory containing at least one ORCA .out file, or paste the file path directly into the dialog box.
2. **Configure export range and step size**:
    - Set the export range (lower to upper bounds in cm<sup>-1</sup>) and the resolution (step size in cm<sup>-1</sup>) for the IR spectra.
3. **Set the FWHM**:
    - Adjust the FWHM for peak broadening. A FWHM of 8 cm<sup>-1</sup> is recommended.
4. **Apply the vibrational scaling factor**:
    - Enter a scaling factor for the vibrational frequencies to account for anharmonicity (if desired). For no scaling, leave at the default value of `1.00`.
5. **Choose normalization and plotting options**:
    - Decide whether to normalize the spectra to unity and if an external plot should be created by checking the respective boxes.
6. **Extract/plot IR spectra**:
    - Click the `Extract/Plot IR spectra` button to begin the extraction process. The module performs Gaussian convolution on vibrational frequencies from each .out file in the specified directory, applies frequency scaling if requested, and compiles an Excel report along with an optional plot.
    - The Excel report, named `output_spectrum_.xlsx` or `output_spectrum_Norm.xlsx` (based on normalization choice), features a wavenumber column and individual columns for each file's IR spectrum, with headers matching the .out file names. Note that the scaling factor and FWHM will be appended to the end of the .xlsx file for later reference.
    - If plotting is enabled, the extracted IR spectra from all .out files will be plotted on a single figure and saved to `IR_spectra_plot.png`; the scaling factor and FWHM will be appended to the end of the file for later reference.

### T10. Extract/plot UV-Vis spectra (VG-FC): 
This module extracts and plots UV-Vis spectra from .spectrum.rootN and/or .spectrum obtained by employing the Excited State Dynamics (ESD) keyword recognized by ORCA; see [T5 ORCA out to ORCA .inp (TD-DFT)](#t5-orca-out-to-orca-inp-vgfctd-dft). While testing was done on ESD jobs employing the Vertical-Gradient method, this module will also work for any .spectrum.rootN and .spectrum files generated using ORCA's ESD function. 

### **Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T10_ORCA_extract_VGFC_Spectra)**: 
1. **Select directory**: 
    - The path to the directory containing `.spectrum.rootN` and/or `.spectrum` files. This can be set manually, pasted directly into the dialog box, or by using the `Browse` button.
2. **Configure input and output units**:
    - Select the energy unit of the data in the .spectrum and/or .spectrum.rootN files (nm, cm<sup>-1</sup>, or eV). Note that this selection determines how the spectral data is interpreted, so ensure the correct unit is selected.
    - Select the energy unit that you wish the spectra to be exported as (nm, cm<sup>-1</sup>, or eV). This choice can be identical to the input unit. 
3. **Set the export range**:
    - Define the range and step size for the exported spectra. Default values, which are auto-updated in the GUI based on the selected `output unit`, are selected to cover the full UV-Vis range
        - Wavelength(nm): 150 - 700 nm
        - Wavenumber(cm<sup>-1</sup>): 14000 - 67000 cm<sup>-1</sup>
        - eV: 1.8 - 8.3 eV
4. **Apply Shifts (Optional)**:
    - Enter a value to shift the spectral data for better comparison with experimental data. For no shift, enter a value of `0.00`. 
    - Shift values can be in nm, cm<sup>-1</sup>, or eV. The spectral shift value is updated automatically with values commonly used in the literature: [Example 1](https://pubs.acs.org/doi/10.1021/acs.jpcb.0c05761?ref=PDF) [Example 2](https://chemistry-europe.onlinelibrary.wiley.com/doi/10.1002/cphc.201402300) [Example 3](https://www.nature.com/articles/s43586-021-00034-1)
        - **Using eV is advised** due to its intrinsic linearity. For example, shifting a spectrum by, for example, -0.25 eV, applies a uniform energy change across the range of energies. In contrast, shifting by, for example, 25 nm or -460 cm<sup>-1</sup> results in variable energy changes depending on the part of the spectrum you're adjusting. To illustrate this, decreasing the wavelength by 25 nm from a photon energy of 4 eV changes it to 3.70 eV (a shift of 0.30 eV), whereas the same nm shift from 3 eV results in 2.83 eV (a shift of 0.27 eV). 
5. **Normalizaiton and external plotting options**: 
    - Choose whether the exported spectra will be normalized to a maximum value of 1 or saved saved to an external .png file by checking the corresponding boxes. If plotting is enabled, you can also specify the number of plots that appear in each row.
6. **Output Basename**: 
    - The basename for the output files (.xlsx and/or .png), which will be augmented with relevant details from the extraction process.
7. **Extract/plot the UV-Vis spectra**:
    - Clicking the `Extract UV-Vis Spectra (VGFC)` initiates the extraction and conversion processes outlined below. First, the script checks the specified directory for .spectrum.root and .spectrum files. 
    - If **both** .spectrum.rootN and .spectrum files files are present:
        1. **File reading**: For each .spectrum.root file, the code reads the data, ensuring that all column headers match the expected titles from an ORCA generated .spectrum.root file.
        2. **Unit conversion and shifting**: If the headers match the expected titles, values in the `Energy` are converted from the input unit to the shift unit. The shift is then added to the transformed energy values, and then the energies are converted to the output unit, yielding `transformned energy`.
        3. **Interpolation**: The data from the `transformed energy` and the `TotalSpectrum` column of each .spectrum.root file is interpolated onto a predefined grid defined by the export range and spacing parameters.
        4. **Output preparation**: The interpolated data is prepared for each .spectrum.root file, and if requested, the total (summed) spectrum is normalized.
        5. **Output generation**: The script writes the processed data to an Excel file, creating a separate sheet for each .spectrum.root file and another excel file for each total (summed/normalized) spectrum and plots the roots and total of each molecule's UV-Vis spectrum as a separate plot on a signle figure (if requested).
    - If **only** .spectrum files files are present:
        1. **File reading**: For each .spectrum file, the code reads the data, ensuring that all column headers match the expected titles from an ORCA generated .spectrum.root file.
        2. **Unit conversion and shifting**: If the headers match the expected titles, values in the `Energy` are converted from the input unit to the shift unit. The shift is then added to the transformed energy values, and then the energies are converted to the output unit, yielding `transformned energy`.
        3. **Interpolation**: The data from the `transformed energy` and the `TotalSpectrum` column of each .spectrum.root file is interpolated onto a predefined grid defined by the export range and spacing parameters.
        4. **Output generation**: The script writes the processed data to an Excel file, writing data from each .spectrum file (optionally normalized) as a new column in the excel file and plots each total spectrum on the same figure (if requested). 

## **T11. Boltzmann-weight CCSs**: 
This module integrates with the outputs of [**T7**](#T7-calculate-thermochemistry), [**T8**](#T8-extract-coupled-cluster), [Mobcal-MPI](https://github.com/HopkinsLaboratory/MobCal-MPI) to calculate the ensemble-averaged CCS of a molecule by Boltzmann-weighting the CCS of each conformation according to its population.

### **Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T11_BW_CCS_Analyzer)**: 
1. **DFT Thermochem .csv**: 
    - .csv file containing DFT electronic energies and thermochemistry, which is generated from the [**T7. Calculate thermochemistry**](#T7-calculate-thermochemistry) tab. Use the Browse button to select the file, or paste the path to `directory/DFT_thermochem_file.csv` directly into the dialog box. 
2. **Coupled cluster .csv (optional)**: 
    - .csv file containing CCSD(T) or CCSD energies generated from the [**T8. Extract coupled cluster energies**](#T8-extract-coupled-cluster) tab. Use the Browse button to select the file, or paste the path to `directory/coupled_cluster_file.csv` directly into the dialog box. To bypass the requirement of coupled cluster energies, simply leave this field blank. Boltzmann-weights will be calculated using DFT energies from the DFT Thermochem .csv file instead. 
3. **CCS .csv from MobCal-MPI 2.0**: 
    - .csv file containing CCSs, which is generated from the [many .mout analyzer](https://github.com/HopkinsLaboratory/MobCal-MPI) function of the MobCal-MPI 2.0 GUI. Use the Browse button to select the file, or paste the path to `directory/CCS_file.csv` directly into the dialog box.
4. **Output .xlsx filename**: 
    - Define the directory and filename of the .xlsx file where the analysis results will be written. Ensure that the specified directory exists and that the filename ends with .xlsx.
5. **Temperature**:
    - The temperature (in Kelvin) at which the Boltzmann-weighting will be performed. Ensure this matches the temperature used for thermochemical corrections in the .csv file generated by [**T7. Calculate thermochemistry**](#T7-calculate-thermochemistry).
6. **Run BW-CCS analysis**
    - Click the `Run BW-CCS Analysis` button to start the analysis. The code will confirm that all the necessary information is provided in each .csv files. Once each .csv file is validated, the BW-CCS is calculated using the following workflow:
        1. **Read .csv inputs**: Read and clean the DFT, coupled cluster (if provided), and CCS .csv files. Cleaning up the headers involves stripping leading and trailing spaces from the column names to ensure consistent access to data fields. 
        2. **Preprocessing**: Ensure that the filenames provided in the filename column is consistent for each .csv file. If there are any mismatches, the user will be notified, and the BW-CCS will not be calculated.
        3. **Gibbs energy calculation**: After confirmation that each .csv file contains information on the **same** files, Gibbs energy are calculated using electronic energies (from DFT or coupled cluster calculations) and thermochemical corrections as per:
            $$G = E_{\text{electronic}} + G_{\text{correction}}$$
            where $E_{\text{electronic}}$ is the electronic energy data and $G_{\text{correction}}$​ is the Gcorr data from the DFT thermochem .csv file.
        4. **Relative Gibbs energy and relative population calculations**: Calculate the Gibbs energy relative to the lowest energy isomer ($\Delta G_{\text{rel}}$), as per:
            $$\Delta G_{\text{rel}} = (G - G_{\text{min}}) \times 2625.5$$        
            where $G_{\text{min}}$ is the lowest Gibbs energy in the dataset.
        then use $\Delta G_{\text{rel}}$ to compute the relative populations ($P_i$) at the specified temperature *T*:
            $$P_i = \frac{\exp\left(-\frac{\Delta G_{\text{rel}, i}}{R T}\right)}{\sum_i \exp\left(-\frac{\Delta G_{\text{rel}, i}}{R T}\right)}$$
            where *R* is the gas constant in kJ/(mol·K) and *T* is the temperature in Kelvin.
        5. **Calculating the Boltzmann-weighted CCS**: The BW-CCS ($CCS_{\text{BW}}$) is computed by weighting the CCS values of each isomer *i* in the ensemble ($\text{CCS}_i$) by their relative population, then summing across all isomers:
            $$CCS_{\text{BW}} = \sum_i (P_i \times \text{CCS}_i)$$
        6. **Calculating the standard deviation of the Boltzmann-weighted CCS**: The standard deviation of the BW-CCS ($\sigma_{\text{BW-CCS}}$) is calculated to provide an estimate of the uncertainty in the weighted CCS value as per:

            $$\sigma_{\text{BW-CCS}} = \sqrt{\sum_i \left(P_i \times \sigma_{\text{CCS}, i}\right)^2}$$
            where $\sigma_{\text{CCS}, i}$ is the standard deviation of the CCS of isomer *i*.
        7. **Output generation**: All parameters calculated above are written to an Excel file.

## **T12. ORCA LED analysis**: 
This module summarizes the results from a Local Energy Decomposition (LED) calculation, providing insight into the nature of intermolecular interactions by decomposing the DLPNO-CCSD(T) energy into physically meaningful contributions. Currently, the code supports two fragments (N = 2). For further details, refer to this [example](https://pubs.acs.org/doi/10.1021/acs.jctc.8b00915). 

### Usage and [example files](https://github.com/HopkinsLaboratory/PodPals/tree/master/Sample_Files/T12_ORCA_LED_Analysis):
1. **Provide DLPNO-CCSD(T) LED .out file of the parent complex**: Provide the path and filename of the .out file containing the output from the LED analysis. , use the Browse buttons to select the respective .out files containing DLPNO-CCSD(T) calculations. This can be set manually, pasted directly into the dialog box, or by using the `Browse` button.
2. **Provide DLPNO-CCSD(T) single point energy .out files for each fragment of the parent complex**: Provide the path and filename of each .out file containing the output from a DLPNO-CCSD(T) energy calculation of each fragment of the parent molecule. This can be set manually, pasted directly into the dialog box, or by using the `Browse` button.
3. **Run LED analysis**: Click the `Run LED Analysis` button to start the calculation. The analysis will read components from each file and calculate the following interactions:
    1. **Repulsive Electronic Preparation ($E_{elprep}$​)**:
        This term represents the energy required to to distort the electronic structures of the isolated fragments from their ground states to the ones they have in the parent complex.
        $$E_{elprep} = E_{XvY_{ref}} - (E_{F1_{ref}} + E_{F2_{ref}})$$
    2. **Intermolecular Electrostatic Interaction ($E_{elstat}$)**:
        This term represents the electrostatic interaction between the electron densities of the fragments in the parent complex, thus providing a quantification to the permanent electrostatic interaction (i.e., the interaction between charges and/or permanent multipoles on different fragments). 
        $$E_{elstat} = \text{Electrostatics contributions from the LED analysis}$$
    3. **Intermolecular exchange interaction($E_{Exch}$)**:
        This term represents the exchange interaction between the electron densities of the fragments in the parent complex.
        $$E_{Exch} = \text{Exchange contributions from the LED analysis}$$
    4. **CCSD correlation energy (dispersion) ($E_{disp}$)**:
        This term represents the dispersion based contribution to the CCSD correlation energy of the interacting fragments within the parent complex
        $$E_{disp} = \text{Dispersion contributions from the LED analysis}$$
    5. **CCSD correlation energy (non-dispersion) ($E_{nondisp}$)**:
        This term represents the non-dispersion based contribution to the CCSD correlation energy of the interacting fragments within the parent complex
        $$E_{nondisp} = E_{nondisp} - (E_{F1_{C_{CCSD}}} + E_{F2_{C_{CCSD}}})$$
    6. **Perturbative triples correlation interaction ($E_{trip}$)**:
        This term represents the energy contribution from the perturbative treatment of triple excitations:
        $$E_{\text{trip}} = E_{Ad_{CT}} - (E_{F1_{CT}} + E_{F2_{CT}})$$
    7. **Total interaction energy ($$E_{tot}$)**:
        This term represents the total interaction energy between the fragments, calculated as the difference between the total energy of the combined system and the sum of the total energies of the individual fragments:
        $$E_{tot} = E_{int} = E_{LED_{tot}} - (E_{F1_{tot}} + E_{F2_{tot}})$$
        or equivalently, 
        $$E_{tot} = E_{elprep} + E_{elstat} + E_{Exch} + E_{disp} + E_{nondisp} + E_{trip}$$

Each term is written to a .csv file called `LED_analysis.csv` as well as to a plot called `LED_analysis.png`. All terms are provided in kJ/mol. 

## T13. ORCA NEB analysis: 
Plots the results from a Nudged Elastic Band (NEB) analysis, including the reaction coordinate during each NEB iteration along several user-specified parameters, including reaction coordinate, atomic distances, bond angles, or dihedral angles. Exporting the *XYZ* coordinates images from a specific run and generation of an ORCA .inp from these coordinates is also supported. Implementation will be including in the next major update. 