The software can be used for data of humans and mice.
If you want to use the software to other animal species,
・	Download the genome for each chromosome from the database, change the extension with “ren *.fa *.txt” at the command prompt, and make data of a cell for each chromosome with chr_fa_to_1cell.mlx.
・	Download the consensus sequences matrix from JASPAR, and convert it to the corresponding Excel file as Score.csv (notice: renumber the duplicates as “-2 or -3” format and change “::” to “-“).
・	Separate each score matrix and save in Score folder.
・	Create Score_name.csv with duplicates removed, Score_name_div.csv with the separated names of transcription factors, and TF_name_list.csv with a list of transcription factor names.
・	Using TF_name_list.csv, summarize the transcription start sites of each transcription factor gene in TF_seq_start_end.txt. If some cannot be batch downloaded, collect them manually. (notice: possibility that the registered gene name may be different from JASPAR).
Change chromosome numbers of X and Y chromosomes into “23” and “24” for human and “20” and “21” for mouse, respectively.
Register all the transcription start sites in the list.
・	If you want to register consensus sequences and transcription factors that are not registered in JASPAR, you can manually register them in Score.csv, Score_name_div.csv, TF_name_list.csv, and TF_seq_start_end.txt.
・	If the TFs are registered as repressor in GO term but not registered in activator, add the TFs as a repressor in the tail column of Score_name_div.csv.
・	If there is not enough information in that species and you want to refer to the DNase- and ATAC-seq information in other species, create a reference of the transcription start sites like as the TF_seq_start_end2_mouse_refhuman_cal.xlsx.

Implementation for each cell type
・	Select the target animal species in Peak Browser from Chip-Atlas (notice: use the same genome downloaded above) Select the following
For Antigen Class: Histone DNase-seq or ATAC-seq, select Antigen: All, set the target cell type, select Threshold for Significance 50, and download the BED file.
・	Put both BED files in the Get_from_Chip_Atlas folder, delete the first line, and change from BED to TXT file.
・	Run Get_from_Chip_Atlas.mlx, with renaming the file in the first section, and delete the unneeded var.
・	Extract the region containing Histone_core and DNase-seq. Output data to Histone_DNase_match_any_cells.xlsx.
・	Run Get_promoter.mlx with Histone_DNase_match_”any_cells”.xlsx as input, output Open_”arbitrary_cells”_promoter.txt, etc. and get promoter and body information.
(If the transcription factors you really need are missing from the DNase-seq, input them manually into Open_”any_cells”_promoter.txt by visually checking the ChIP-Atlas website).
・	Open_”any_cells”_promoter_bindseq.txt contains a list of transcription factors whose promoters are active. Using this name, create Open_DegIndex_list.txt (P for protein degradation, R for mRNA degradation, the higher number will be highly degraded), Open_initial_value_list.txt, Open_Kd_list.txt. 
・	Initial value is the normalized RNA-seq data (recommend to set the mean is 10)
・	Kd is set to 1 or 10.
・	In Open_DegIndex_list.txt, set the degradation value of P to twice the initial value (default is 20), which will be changed in combination with the amount of transcription factor increase.

Necessary input files for each cell
Open_DegIndex_list.txt, Open_initial_value_list.txt, Open_Kd_list.txt, Open_any_cell_promoter_bindseq.txt and Open_any_cell_body_bindseq.txt, Score_name_div.csv

Required folders (Separate the folder for each cell).
Promoter (empty)
Body (empty)
Score (copy including contents)
TF_score (empty)
TF_score2 (empty)
Repressor_score (empty)
Repressor_score2 (empty)

・	Run TF_binding.mlx and calculate the scores of promoter and gene body of each gene and transcription factors that bind to the promoter and gene body with Open_”any_cell”_promoter_bindseq.txt and Open_”any_cell”_body_bindseq.txt as input. Output in Promoter and Body folders.
・	Run Act_Rep_score_making3.mlx with Open_TF_Kd_list.txt and Score_name_div.csv as input. Output in TF_score, TF_score2, Repressor_score, and Repressor_score2
・	Run Score_to_TF to make Score_to_TF.txt
・	Create folders in the Input folder with the name of the condition you need, and copy Open_DegIndex_list.txt, Open_initial_value_list.txt, and Open_Kd_list.txt
.	
How to change the Kd value with reference of the gene expression.
・	Add Ref_value_list.txt of the reference gene expression and Fix_Kd_list.txt in the Input folder. (notice: the number of genes can be changed)
・	The optimal Kd is calculated using ISNA program with m=20 or so using the xexp and Kd values that are considered optimal.

% xexp = exponent for consensus sequence (recommend: 10)
% x = constant for TF (recommend: 1)
% m = passage for TF concentration (recommend: 20)
% th = threshold of consensus score (recommend: 0.7)
% thSE = threshold of super enhancer (recommend: 3.2)
% gact = weight of regulation for Kd of activator (recommend: 1)
% grep = weight of regulation for Kd of repressor (recommend: 1)
% g2 = range of constant Kd (recommend: 1.01)
% gw = passage increase weight for Kd (recommend: 1)
% Kdmax = maximum value of Kd (recommend: 100)
% Kdmin = minimum value of Kd (recommend: 0.01)
% i = export file number

Addition of other target genes
・	Add the transcription start sites of each target gene in Target_seq_start_end.txt file.
・	Run Get_promoter.mlx with Histone_DNase_match_any_cells.xlsx as input and move them to the folder for each cell.
・	Remove TFs from Open_ DegIndex_list.txt and create Target_list.txt.
・	Run TF_binding.mlx and use Open_”any_cell”_target_promoter_bindseq_seq.txt and Open_”any_cell”_target_body_bindseq.txt as input to calculate TF-binding to the promoter and gene body of each gene.
・	Run Act_Rep_score_making3.mlx with Target_list.txt and Open_”any_cell”_target_body_bindseq.txt as input.
・	Add Target_list to the end of the list in Open_DegIndex_list.txt.
・	In Check_pro_body.txt, add “1” for open promoter with open gene boy and “0” without open gene body following the order to Open_DegIndex_list.txt.
・	Add the initial values of the target genes in Open_initial_value_list.txt.