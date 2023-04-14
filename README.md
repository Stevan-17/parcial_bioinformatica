# parcial_bioinformatica
## Punto 1 

**Descargué el PopSet de manera local, lo modifiqué y lo subí a mi carpeta**
Expresión regular: 
	Find: ```(>\w+.\w+) (\w+) (\w+)*.+```
	Replace: ``` $1_COI_$2_$3```

```scp -i bio.pt.pem -P 37022 /mnt/c/Users/Surface/Desktop/COI_sequences.fasta bio.pt@172.25.255.10:/home/bio.pt/data/Parcial1/parcial1_CamiloPeralta```

**Descarga de la secuencia query**
wget https://raw.githubusercontent.com/paula-torres/bioinformatica_ur/main/files/query_parcial.fasta

**Construcción base de datos Blastn**
``` 
salloc
module load blast/2.7.1

makeblastdb -in COI_sequences.fasta -dbtype nucl -parse_seqids -out COI_sequences_db -title "COI_sequences_database"

blastn -query query_parcial.fasta -task megablast -db COI_sequences_db -outfmt 7 -word_size 7 -out blast_COI -num_threads 1
```

**Interpretación Blast**

Considero que la query sequence que buscamos en nuestra base de datos corresponde a _Spodoptera cilium_, debido principalmente a que la mayoría de sus secuencias tienen un E-value < 1, incluso algunos alcanzan valores < 0.1 e incluso valores de 0 acompañados de un porcentaje de identidad > 85%, indicando conservación evolutiva de estas secuencias. Por el contrario, las secuencias de _Mythimna loreyi_ y _Peridromasaucia_ presentan E-values > a 23, e incluso > 900 indicando que, a pesar de que tenga un porcentaje de identidad elevado, esto tiene una alta probabilidad de deberse al azar en vez de a la conservación de estas secuencias. 

## Punto 2 

**Concatenar PopSet y query**
``` 
COI_sequences.fasta query_parcial.fasta > COI_all_sequences.fasta
```

**Alineamiento múltiple**
```
module load muscle/3.8.31

muscle -in COI_sequences.fasta -out COI_alignment.fasta


```

**Árbol de máxima verosimilitud**
```
# Crear matriz 
cp FASconCAT-G_v1.05.1.pl /home/bio.pt/data/Parcial1/parcial1_CamiloPeralta/punto_2

# Árbol con IqTree

module load iqtree/1.6.12
iqtree -s FcC_supermatrix.nex -m GTR+I+G -bb 1000 -pre COI_muscle_IqTree

# Descarga del servidor a mi pc

scp -r -i bio.pt.pem -P 37022  bio.pt@login01-hpc.urosario.edu.co:/home/bio.pt/data/Parcial1/parcial1_CamiloPeralta/punto_2/ML_tree/COI_muscle_IqTree.treefile /mnt/c/Users/Surface/Desktop/

```
**Arbolito con FigTree enraizado por punto medio** 

![COI_muscle_IqTree treefile](https://user-images.githubusercontent.com/130588867/232134292-b0e4de97-eda7-479a-8768-be803ac2bfef.jpg)


Este árbol nos permite ver que cada especie se encuentra separada de las demás, haciendo énfasis en la separación de las mismas. Además, se muestra que _S. cilium_ y _S. exigua_ compartieron un mismo ancestro común, por lo cual forman un grupo monofilético al igual que _M. loreyi_ y _P. saucia_. Estos dos grupos monofiléticos indican una mayor cercanía evolutiva entre las especies que componen a cada grupo a comparación de las demás especies. 

## Punto 3 

```
wget https://raw.githubusercontent.com/paula-torres/bioinformatica_ur/main/files/archivo1.csv

awk -F "," '{print NR; exit}' archivo1.csv # Número de Columnas: 4

wc -l archivo1.csv # Número de filas: 49

wc -m archivo1.csv # Número de caracteres: 908

sed 's/chrom/Cromosoma/' archivo1.csv > result_file.bed 
## Como 'chrom' no existe, lo modifico por chr

sed 's/chr/Cromosoma/' archivo1.csv > result_file.bed 

## Modificar comas por tabulaciones
sed -i 's/,/\t/g' result_file.bed

## Filtrar por "coding"

grep -w "coding" result_file.bed | sed 's/^[[:space:]]*//' > result_file_coding.bed
```

## Punto 4 

```
## Comprimir mi carpeta 

zip -r parcial_CamiloPeralta.zip parcial1_CamiloPeralta/

## Descargar .zip
scp -r -i bio.pt.pem -P 37022  bio.pt@login01-hpc.urosario.edu.co:/home/bio.pt/data/Parcial1/parcial_CamiloPeralta.zip /mnt/c/Users/Surface/Desktop/
