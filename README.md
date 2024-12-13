# Files .nc conversion to .TIFF

## Conversion of .NC files to tiff files for exchanging use in GIS software

#####################################################################
# Instalar los paquetes necesarios (si no están instalados)
if (!require("rgdal")) install.packages("rgdal", dependencies = TRUE)
if (!require("ncdf4")) install.packages("ncdf4", dependencies = TRUE)
if (!require("raster")) install.packages("raster", dependencies = TRUE)

library(rgdal)
library(ncdf4)
library(raster)

# Función para convertir de NetCDF a TIFF
convert_nc_to_tiff <- function(nc_file) {
  # Abrir el archivo NetCDF
  nc_data <- nc_open(nc_file)
  
  # Listar las variables disponibles en el archivo NetCDF
  variables <- names(nc_data$var)
  message(paste("Variables disponibles en", nc_file, ":", toString(variables)))
  
  # Usar la primera variable disponible (o especificar manualmente si se desea)
  var_name <- variables[1]
  message(paste("Usando la variable:", var_name))
  
  # Obtener la variable del archivo NetCDF
  raster_data <- raster(nc_file, varname = var_name)
  
  # Crear el nombre del archivo TIFF usando solo el nombre de la variable
  output_tiff <- file.path(dirname(nc_file), paste0(var_name, ".tiff"))
  
  # Guardar como TIFF
  writeRaster(raster_data, output_tiff, format = "GTiff", overwrite = TRUE)
  
  # Cerrar el archivo NetCDF
  nc_close(nc_data)
  
  message(paste("Archivo convertido:", output_tiff))
}

# Obtener todos los archivos .nc en la carpeta especificada
process_nc_files <- function(folder_path) {
  nc_files <- list.files(path = folder_path, pattern = "\\.nc$", full.names = TRUE)
  
  # Verificar si se encontraron archivos
  if (length(nc_files) > 0) {
    message(paste("Se encontraron", length(nc_files), "archivos .nc. Iniciando conversión..."))
    for (nc_file in nc_files) {
      convert_nc_to_tiff(nc_file)
    }
  } else {
    message("No se encontraron archivos .nc en la carpeta especificada.")
  }
}

# Especificar la carpeta que contiene los archivos NetCDF
folder_path <- "D:/BASE/environmental_layers"

# Ejecutar la conversión
process_nc_files(folder_path)
