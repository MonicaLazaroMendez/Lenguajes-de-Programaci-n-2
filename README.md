#include <iostream>
#include <string>
#include <algorithm>
#include <sstream>
#include <iomanip>

using namespace std;

// Función para limpiar acentos y convertir a mayúsculas
string limpiar_nombre(const string& str) {
    string temp = str;
    // Convierte a mayúsculas
    transform(temp.begin(), temp.end(), temp.begin(), ::toupper);

    // Reemplaza acentos
    for (size_t i = 0; i < temp.length(); ++i) {
        if (temp[i] == 'Á') temp[i] = 'A';
        else if (temp[i] == 'É') temp[i] = 'E';
        else if (temp[i] == 'Í') temp[i] = 'I';
        else if (temp[i] == 'Ó') temp[i] = 'O';
        else if (temp[i] == 'Ú') temp[i] = 'U';
        else if (temp[i] == 'Ñ') temp[i] = 'N'; // La Ñ se trata con reglas especiales, pero la simplificamos a N
    }

    return temp;
}

// Función para obtener la primera vocal después de la inicial
char obtener_primera_vocal(const string& apellido) {
    for (size_t i = 1; i < apellido.length(); ++i) {
        char c = apellido[i];
        if (c == 'A' || c == 'E' || c == 'I' || c == 'O' || c == 'U') {
            return c;
        }
    }
    return 'X'; // En caso de no encontrar vocal (solo como fallback)
}

string generar_rfc_sin_homoclave(string nombre, string ap_paterno, string ap_materno, int dia, int mes, int anio) {
    // 1. Limpieza de datos
    nombre = limpiar_nombre(nombre);
    ap_paterno = limpiar_nombre(ap_paterno);
    ap_materno = limpiar_nombre(ap_materno);

    // 2. Obtener la parte alfabética (4 caracteres)
    string rfc_alfabetico = "";

    // Primera letra del Apellido Paterno
    rfc_alfabetico += ap_paterno[0]; // L de LÁZARO

    // Primera vocal del Apellido Paterno que no sea la inicial
    rfc_alfabetico += obtener_primera_vocal(ap_paterno); // A de LÁZARO

    // Primera letra del Apellido Materno
    rfc_alfabetico += ap_materno[0]; // M de MÉNDEZ

    // Primera letra del Nombre
    rfc_alfabetico += nombre[0]; // M de MÓNICA
    
    // El resultado hasta aquí es: LAMM

    // 3. Obtener la parte numérica (6 caracteres - AAMMDD)
    stringstream ss;
    
    // Año (AA) - Últimos dos dígitos
    ss << setfill('0') << setw(2) << (anio % 100); // 98
    
    // Mes (MM)
    ss << setfill('0') << setw(2) << mes; // 03
    
    // Día (DD)
    ss << setfill('0') << setw(2) << dia; // 15
    
    string rfc_numerico = ss.str(); // 980315

    // 4. Resultado final (10 caracteres)
    return rfc_alfabetico + rfc_numerico;
}

int main() {
    // Datos de la persona
    string nombre = "Mónica";
    string ap_paterno = "Lázaro";
    string ap_materno = "Méndez"; 
    int dia = 15;
    int mes = 3; // Marzo
    int anio = 1998;

    // Ejecutar la generación del RFC
    string rfc = generar_rfc_sin_homoclave(nombre, ap_paterno, ap_materno, dia, mes, anio);

    // Salida
    cout << "### Generador RFC (Sin Homoclave) ###" << endl;
    cout << "-----------------------------------------------" << endl;
    cout << "Nombre Completo: " << nombre << " " << ap_paterno << " " << ap_materno << endl;
    cout << "Fecha de Nacimiento: " << dia << "/" << mes << "/" << anio << endl;
    cout << "-----------------------------------------------" << endl;
    cout << "**RFC (10 caracteres sin Homoclave):** " << rfc << endl;

    return 0;
}
