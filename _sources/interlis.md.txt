# Interlis

Antes de entrar en detalles sobre Interlis, es preciso plantear el siguiente escenario:

**Escenario:**

Dos organizaciones del sector de la salud, la organización A y la organización B, requieren intercambiar información para usar en sus procesos (**interoperabilidad**). Entre toda la información que van a intercambiar, están los datos de sus *pacientes*.

En el *modelo de datos* de la organización A, los pacientes se almacenan con el nombre **Persona**, mientras que en la organización B son almacenados con el nombre de **Cliente**. Además de esto, los nombres de los pacientes se almacenan con una estructura ligeramente diferente (ver la imagen).

<a class="" data-lightbox="Modelo organizaciones" href="./_static/organizaciones.png" title="Modelo organizaciones" data-title="Modelo organizaciones"><img src="./_static/organizaciones.png" class="align-center" alt="./_static/organizaciones.png"/></a>

A pesar de ser el mismo concepto, estas pequeñas diferencias en los nombres, en las estructuras y posiblemente en los tipos de datos, dificulta el objetivo de intercambiar la información. Para lograr dicho objetivo, las organizaciones deben de definir una **semántica común** y un formato con el que se va a transferir la información. Por lo cuál se plantean los siguientes interrogantes: ¿Cómo definir una semántica común? ¿Qué formato de transferencia elegir?

## ¿Qué es Interlis?

Interlis es un estándar (SN612031) cuyo objetivo es resolver los interrogantes que plantea el escenario descrito anteriormente, dado que fue pensado para buscar la **interoperabilidad** entre Sistemas de Información independientemente de la plataforma que estos utilizan. Concretamente, Interlis es la combinación de dos elementos:

1. **Un lenguaje**[^def_lenguaje] para la descripción de modelos con el que se busca definir una semántica común
2. **Un formato de transferencia**[^def_xtf] unificado que se deriva de los modelos descritos con el lenguaje de Interlis

**¿Qué quiere decir esto?**

En el escenario planteado inicialmente, si las organizaciones utilizan Interlis, pueden definir una semántica común a través de un modelo descrito en el **lenguaje de Interlis**, como por ejemplo el modelo a continuación:

```interlis
INTERLIS 2.3;

MODEL Modelo_pacientes_ejemplo (es)
AT "mailto:contacto@swistierrascolombia.com"
VERSION "1.0"  =

  TOPIC Salud =
    CLASS Paciente =
      nombres : MANDATORY TEXT*30;
      apellidos : MANDATORY TEXT*30;
      documento_identidad : MANDATORY TEXT*100;
      direccion: TEXT*255;
    END Paciente;

    !! otras definiciones ...
    
  END Salud;
END Modelo_pacientes_ejemplo.
```

Con este modelo las organizaciones A y B no solo llegan a un acuerdo con respecto a la información que van a compartir (los nombres, tipos de datos, campos obligatorios, entre otras cosas), sino que también define el formato con el que se transfiere la información.

**¿Cómo la descripción del modelo también especifica el formato de transferencia?** 

El formato de transferencia de Interlis esta escrito en lenguaje XML. La estructura de este, a su vez, es formada por las estructuras y relaciones entre clases que contiene el modelo. De esta forma, un registro que pertenece a cierta clase es envuelto en una etiqueta XML con el nombre de la clase a la que pertenece, y los valores de cada atributo son envueltos en etiquetas con el nombre del atributo al que pertenecen.

Para el ejemplo, se puede apreciar que el registro de la paciente *Martina González* se encuentra entre la etiqueta XML *«Modelo_pacientes_ejemplo.Salud.Paciente»* con sus atributos también dentro de etiquetas con los nombres especificados en el modelo de Interlis:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<TRANSFER xmlns="http://www.interlis.ch/INTERLIS2.3">
<HEADERSECTION SENDER="ili2pg-4.4.3-658b7daf37ba45ed2330ca3e3a3c3d59c96e91fa" VERSION="2.3">
    <MODELS>
        <MODEL NAME="Modelo_pacientes_ejemplo" VERSION="1.0" URI="mailto:contacto@swistierrascolombia.com">
        </MODEL>
    </MODELS>
</HEADERSECTION>
<DATASECTION>

<Modelo_pacientes_ejemplo.Salud BID="Modelo_pacientes_ejemplo.Salud">
    
    <Modelo_pacientes_ejemplo.Salud.Paciente TID="1">
        <nombres>Martina</nombres>
        <apellidos>González</apellidos>
        <documento_identidad>79111222</documento_identidad>
        <direccion>cra 200 # 20-40</direccion>
    </Modelo_pacientes_ejemplo.Salud.Paciente>
    
    <Modelo_pacientes_ejemplo.Salud.Paciente TID="2">
        <nombres>Enrique</nombres>
        <apellidos>Ospina</apellidos>
        <documento_identidad>1020123456</documento_identidad>
    </Modelo_pacientes_ejemplo.Salud.Paciente>
</Modelo_pacientes_ejemplo.Salud>
</DATASECTION>
</TRANSFER>
```

## El lenguaje de Interlis

El lenguaje de Interlis, como se vio en las anteriores secciones, se utiliza para describir modelos de la realidad y definir una semántica común. Es un lenguaje orientado a objetos que es conceptualmente muy similar a los diagramas de clases de UML, pero que a diferencia de estos, los modelos descritos en Interlis son precisos y pueden ser interpretados de forma inequívoca.

**¿Cómo se logra esta precisión en la descripción de los modelos?**

Esto es posible gracias a que el lenguaje de Interlis es un lenguaje de texto que contiene definiciones claras para los tipos de datos básicos, los tipos de geometría, las relaciones entre clases,  la definición de enumeraciones (dominios) entre otros elementos, de tal forma que el lenguaje puede ser interpretado por diferentes herramientas de software y transformado a distintos Sistemas Gestores de Bases de Datos. Por estas razones, *“el lenguaje se ofrece como un complemento necesario a UML”*.

## El formato de transferencia de Interlis

El formato de transferencia de Interlis está escrito en lenguaje XML y su estructura está definida por los modelos escritos en el lenguaje Interlis, como se había mencionado. Cuando se va a **transferir información**, estos XML son generados automáticamente con algunas aplicaciones de software, que se encargan de recopilar lo datos de las bases de datos y escribirlos en los archivos de salida (XML con extensión **xtf**), con el formato adecuado. 

Como los archivos generados deben ser conformes al modelo descrito en Interlis, es posible verificar que estos tengan la estructura de los datos, las relaciones y su cardinalidad y demás reglas consignadas en los modelos de forma automática. Esta es una **validación de datos** mucho más potente que la que se podría hacer con el XSD asociado.

[^def_lenguaje]: [Manual de Interlis](https://www.interlis.ch/download/interlis2/ili2-refman_2006-04-13_e.pdf) capítulo 2
[^def_xtf]: [Manual de Interlis](https://www.interlis.ch/download/interlis2/ili2-refman_2006-04-13_e.pdf) capítulo 3
