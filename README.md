#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_CANDIDATOS 10 // Máximo número de candidatos
#define MAX_NOMBRE 50 // Máximo tamaño para el nombre del candidato
#define MAX_RECINTOS 6 // Número de recintos

// Estructura para almacenar los votos de cada candidato
typedef struct {
    char nombre[MAX_NOMBRE];
    int votos[MAX_RECINTOS];
} Candidato;

int main(int argc, char *argv[]) {
    printf("Hello \n");
    
    FILE *dataFile = fopen("data.csv", "r");
    if (dataFile == NULL) {
        printf("Error abriendo el archivo\n");
        exit(-1);
    }
    
    // Ignorar la primera línea (encabezados)
    char buffer[1024];
    fgets(buffer, 1024, dataFile);
    
    Candidato candidatos[MAX_CANDIDATOS];
    int numCandidatos = 0; // Contador de candidatos
    
    // Leer el archivo línea por línea
    while (fgets(buffer, 1024, dataFile)) {
        // Usar strtok para dividir la línea por comas
        char *token = strtok(buffer, ",");
        strcpy(candidatos[numCandidatos].nombre, token); // Primera parte es el nombre
        
        int recinto = 0;
        while (token != NULL && recinto < MAX_RECINTOS) {
            token = strtok(NULL, ",\n"); // Siguiente token (voto)
            if (token != NULL) {
                candidatos[numCandidatos].votos[recinto++] = atoi(token); // Convertir a entero y almacenar
            }
        }
        
        numCandidatos++; // Incrementar contador de candidatos
    }
    
    // Imprimir los datos para verificar
    for (int i = 0; i < numCandidatos; i++) {
        printf("%s: ", candidatos[i].nombre);
        for (int j = 0; j < MAX_RECINTOS; j++) {
            printf("%d ", candidatos[i].votos[j]);
        }
        printf("\n");
    }
    
    // Cerrar el archivo
    fclose(dataFile);
    
    printf("Archivo leido y datos almacenados \n");
    // Calcular votos por recinto
    int votosPorRecinto[MAX_RECINTOS] = {0};
    for (int i = 0; i < numCandidatos; i++) {
        for (int j = 0; j < MAX_RECINTOS; j++) {
            votosPorRecinto[j] += candidatos[i].votos[j];
        }
    }

     // Calcular el total de votos
    int totalVotos = 0;
    for (int i = 0; i < MAX_RECINTOS; i++) {
        totalVotos += votosPorRecinto[i];
    }
    
    // Calcular el total de votos de cada candidato
    int totalVotosCandidato[MAX_CANDIDATOS] = {0};
    for (int i = 0; i < numCandidatos; i++) {
        for (int j = 0; j < MAX_RECINTOS; j++) {
            totalVotosCandidato[i] += candidatos[i].votos[j];
        }
    }

    // Determinar el ganador general
    int maxVotos = 0;
    char ganadorGeneral[MAX_NOMBRE];
    for (int i = 0; i < numCandidatos; i++) {
        if (totalVotosCandidato[i] > maxVotos) {
            maxVotos = totalVotosCandidato[i];
            strcpy(ganadorGeneral, candidatos[i].nombre);
        }
    }

    // Imprimir resultados
    printf("Votos emitidos en cada recinto:\n");
    for (int i = 0; i < MAX_RECINTOS; i++) {
        printf("Recinto %d: %d votos\n", i + 1, votosPorRecinto[i]);
    }

    printf("Total de votos emitidos: %d\n", totalVotos);

    printf("Ganador general: %s\n", ganadorGeneral);

    printf("Votos emitidos por cada candidato:\n");
    for (int i = 0; i < numCandidatos; i++) {
        printf("%s: %d votos\n", candidatos[i].nombre, totalVotosCandidato[i]);
    }
 system("pause");
    return 0;
}
