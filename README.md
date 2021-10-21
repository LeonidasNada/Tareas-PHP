<?php
// se incluyen esta tabla de  códigos de error que produce la subida de archivos en PHPP
// Posibles errores de subida segun el manual de PHP
$codigosErrorSubida= [ 
    UPLOAD_ERR_OK         => 'Subida correcta',  // Valor 0
    UPLOAD_ERR_INI_SIZE   => 'El tamaño del archivo excede el admitido por el servidor',  // directiva upload_max_filesize en php.ini
    UPLOAD_ERR_FORM_SIZE  => 'El tamaño del archivo excede el admitido por el cliente',  // directiva MAX_FILE_SIZE en el formulario HTML
    UPLOAD_ERR_PARTIAL    => 'El archivo no se pudo subir completamente',
    UPLOAD_ERR_NO_FILE    => 'No se seleccionó ningún archivo para ser subido',
    UPLOAD_ERR_NO_TMP_DIR => 'No existe un directorio temporal donde subir el archivo',
    UPLOAD_ERR_CANT_WRITE => 'No se pudo guardar el archivo en disco',  // permisos
    UPLOAD_ERR_EXTENSION  => 'Una extensión PHP evito la subida del archivo',  // extensión PHP
]; 
$mensaje = '';


var_dump($_FILES);


$ruta = '/home/alumnoa/tallerphp/imgusers';

$numArchivos = 0;
$ficheros = $_FILES['archivos']['name'];
$numArchivos = count($ficheros);

$totalEspacio;

// No se recibe nada, error al enviar el POST, se supera post_max_size
for($i=0; $i<$numArchivos; $i++){
    if (count($_POST) == 0 ){
    $mensaje= "  Error: se supera el tamaño máximo de un petición POST ";
    }

 else 
    { // se reciben el directorio de alojamiento y el archivo
    // Información sobre el archivo subido
    $nombreFichero   =   $_FILES['archivos']['name'][$i];
    $tipoFichero     =   $_FILES['archivos']['type'][$i];
    $tamanioFichero  =   $_FILES['archivos']['size'][$i];
    $temporalFichero =   $_FILES['archivos']['tmp_name'][$i];
    $errorFichero    =   $_FILES['archivos']['error'][$i];

    $mensaje .= 'Intentando subir el archivo: ' . ' <br />';
    $mensaje .= "- Nombre: $nombreFichero" . ' <br />';
    $mensaje .= '- Tamaño: ' . number_format(($tamanioFichero / 100000), 1, ',', '.'). ' KB <br />';
    $mensaje .= "- Tipo: $tipoFichero" . ' <br />' ;
    $mensaje .= "- Nombre archivo temporal: $temporalFichero" . ' <br />';
    $mensaje .= "- Código de estado: $errorFichero" . ' <br />';
    
    $mensaje .= '<br />RESULTADO<br />';

    // Obtengo el código de error de la operación, 0 si todo ha ido bien
        if ($tamanioFichero<=200000){
            
            if ($errorFichero > 0) {
                $mensaje .= "Se ha producido el error nº $errorFichero: <em>" 
                            . $codigosErrorSubida[$errorFichero] . '</em> <br />';
            } else { // subida correcta del temporal
                // si es un directorio y tengo permisos     
                if ( is_dir($ruta) && is_writable ($ruta)) { 
                    //Intento mover el archivo temporal al directorio indicado
                    if (move_uploaded_file($temporalFichero,  $ruta .'/'. $nombreFichero) == true) {
                        $mensaje .= 'Archivo guardado en: ' . $ruta.'/'. $nombreFichero . ' <br />';
                    } else {
                        $mensaje .= 'ERROR: Archivo no guardado correctamente <br />';
                    }
                } else {
                    $mensaje .= 'ERROR: No es un directorio correcto o no se tiene permiso de escritura <br />';
                }
            }
        }else $mensaje.="El archivo pesa más de 200 kbs";
    }
}
?>
<?=$mensaje;?>
