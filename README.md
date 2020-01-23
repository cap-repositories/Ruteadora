# Ruteadora
Este repositorio esta pensado para que las personas que quieran conocer o tengan la posibilidad de usar la ruteadora del CAP, se documenten acerca de su funcionamiento y puedan aprender a usarla de forma independiente.

## Componentes
*hablar acerca de las partes de la ruteadora, mostrar imagenes y mencionar el software que se encuentra instalado*

## Software de control
Esta ruteadora es pensada para trabajar principalmente en la fabricación de PCBs, por lo tanto se recomienda la instalación de [OpenCNCPilot](https://github.com/martin2250/OpenCNCPilot) que envia el codigo G al controlador de la ruteadora a través de un puerto serial.

## Guía para realizar una PCB prototipo
Para realizar un circuito impreso utilizando la ruteadora se deben realizar los siguientes pasos:

0. [Consideraciones generales](#sec_notes)
1. [Crear el esquematico y generar el archivo en formato gerber](#sec_esq)
2. [Generar el codigo G a partir del archivo en formato gerber](#sec_gcode)
3. [Preparar la ruteadora](#sec_prepare)
4. [Ejecutar el codigo G generado](#sec_execute)


Como ejemplo, se mostrará como se realizó una PCB prototipo en la cual se instalan dos conectores USB hembra, uno tipo A y el otro tipo B, para crear una interfaz entre un cable con terminal USB A macho y otro con terminal USB B macho.

### <a name="sec_notes"></a> Consideraciones generales

### <a name="sec_esq"></a> Crear el esquematico y generar el archivo en formato gerber
Para crear el esquematico se puede utilizar cualquier software de automatización de diseño electrónico (**EDA**) como *Altium*, *Eagle*, *KiCad*, *EasyEDA*, entre otros... lo importante es poder diseñar un circuito y generar un archivo en formato [gerber](https://es.wikipedia.org/wiki/Gerber_(formato_de_archivo)) que contiene la información necesaria para fabricar la PCB.

### <a name="sec_gcode"></a> Generar el codigo G a partir del archivo en formato gerber

### <a name="sec_prepare"></a> Preparar la ruteadora

### <a name="sec_execute"></a> Ejecutar el codigo G generado
