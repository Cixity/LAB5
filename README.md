""
Într-un hotel, se dorește gestionarea eficientă a rezervărilor, incluzând adăugarea, vizualizarea,
căutarea după ID, ștergerea și sortarea acestora. Fiecare rezervare include informații despre client, tipul
camerei, numărul de nopți și prețul total al rezervării. Sistemul trebuie să permită efectuarea acestor operații
într-un mod organizatși să susțină salvarea și încărcarea datelor din fișiere.

Lucrarea are scopul de a familiariza studenții cu utilizarea pointerilor și a structurilor de date pentru
gestionarea unui sistem de rezervări într-un hotel, realizând operații de adăugare, căutare, ștergere și sortare
a rezervărilor, precum și gestionarea fișierelor.
""

#include <iostream>
#include <cstring>

using namespace std;

struct Rezervare {
    int id;
    char numeClient[50];
    char tipCamera[20];
    int nopti;
    float pretTotal;
};

// Function prototypes
void afisareMeniu();
void adaugaRezervare(Rezervare*& rezervari, int& numarRezervari, int& capacitate);
void afiseazaRezervari(const Rezervare* rezervari, int numarRezervari);
void cautaRezervareDupaID(const Rezervare* rezervari, int numarRezervari);
void stergeRezervare(Rezervare* rezervari, int& numarRezervari);
void sorteazaDupaPret(Rezervare* rezervari, int numarRezervari);
void sorteazaDupaNume(Rezervare* rezervari, int numarRezervari);
void filtreazaRezervariPremium(const Rezervare* rezervari, int numarRezervari);
void actualizeazaRezervare(Rezervare* rezervari, int numarRezervari);
float calculeazaVenituriTotale(const Rezervare* rezervari, int numarRezervari);
void salveazaInFisier(const Rezervare* rezervari, int numarRezervari);
void incarcaDinFisier(Rezervare*& rezervari, int& numarRezervari, int& capacitate);
bool validareDate(int nopti, float pretTotal);
void redimensioneazaArray(Rezervare*& rezervari, int& capacitate, int numarRezervari);

int main() {
    int capacitate = 10, numarRezervari = 0;
    Rezervare* rezervari = new Rezervare[capacitate];
    int optiune;

    while (true) {
        afisareMeniu();
        cout << "\nAlege o opțiune: ";
        cin >> optiune;

        switch (optiune) {
            case 1:
                adaugaRezervare(rezervari, numarRezervari, capacitate);
                break;
            case 2:
                afiseazaRezervari(rezervari, numarRezervari);
                break;
            case 3:
                cautaRezervareDupaID(rezervari, numarRezervari);
                break;
            case 4:
                stergeRezervare(rezervari, numarRezervari);
                break;
            case 5:
                sorteazaDupaPret(rezervari, numarRezervari);
                break;
            case 6:
                sorteazaDupaNume(rezervari, numarRezervari);
                break;
            case 7:
                filtreazaRezervariPremium(rezervari, numarRezervari);
                break;
            case 8:
                actualizeazaRezervare(rezervari, numarRezervari);
                break;
            case 9:
                cout << "Venituri totale: " << calculeazaVenituriTotale(rezervari, numarRezervari) << " lei\n";
                break;
            case 10:
                salveazaInFisier(rezervari, numarRezervari);
                break;
            case 11:
                incarcaDinFisier(rezervari, numarRezervari, capacitate);
                break;
            case 0:
                delete[] rezervari;
                return 0;
            default:
                cout << "Opțiune invalidă!\n";
        }
    }
}

void afisareMeniu() {
    cout << "\n===== SISTEM DE GESTIONARE A REZERVĂRILOR HOTELIERE =====";
    cout << "\n1. Adaugă rezervare";
    cout << "\n2. Afișează rezervările";
    cout << "\n3. Caută rezervare după ID";
    cout << "\n4. Șterge rezervare";
    cout << "\n5. Sortează după preț";
    cout << "\n6. Sortează după nume client";
    cout << "\n7. Filtrează rezervări premium (>500 lei)";
    cout << "\n8. Actualizează rezervare";
    cout << "\n9. Calculează venituri totale";
    cout << "\n10. Salvează în fișier";
    cout << "\n11. Încarcă din fișier";
    cout << "\n0. Ieșire";
}

void redimensioneazaArray(Rezervare*& rezervari, int& capacitate, int numarRezervari) {
    if (numarRezervari >= capacitate) {
        capacitate *= 2;
        Rezervare* temp = new Rezervare[capacitate];
        for (int i = 0; i < numarRezervari; i++) {
            temp[i] = rezervari[i];
        }
        delete[] rezervari;
        rezervari = temp;
    }
}

bool validareDate(int nopti, float pretTotal) {
    if (nopti <= 0) {
        cout << "Eroare: Numărul de nopți trebuie să fie pozitiv!\n";
        return false;
    }
    if (pretTotal <= 0) {
        cout << "Eroare: Prețul total trebuie să fie pozitiv!\n";
        return false;
    }
    return true;
}

void adaugaRezervare(Rezervare*& rezervari, int& numarRezervari, int& capacitate) {
    redimensioneazaArray(rezervari, capacitate, numarRezervari);

    cout << "ID: ";
    cin >> rezervari[numarRezervari].id;
    
    // Verificare ID unic
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].id == rezervari[numarRezervari].id) {
            cout << "Eroare: ID-ul rezervării trebuie să fie unic!\n";
            return;
        }
    }
    
    cout << "Nume client: ";
    cin.ignore();
    cin.getline(rezervari[numarRezervari].numeClient, 50);
    
    cout << "Tip cameră: ";
    cin.getline(rezervari[numarRezervari].tipCamera, 20);
    
    cout << "Număr de nopți: ";
    cin >> rezervari[numarRezervari].nopti;
    
    cout << "Preț total: ";
    cin >> rezervari[numarRezervari].pretTotal;
    
    if (validareDate(rezervari[numarRezervari].nopti, rezervari[numarRezervari].pretTotal)) {
        numarRezervari++;
        cout << "Rezervare adăugată cu succes!\n";
    }
}

void afiseazaRezervari(const Rezervare* rezervari, int numarRezervari) {
    if (numarRezervari == 0) {
        cout << "Nu există rezervări!\n";
        return;
    }
    
    cout << "\n===== LISTA REZERVĂRILOR =====\n";
    cout << "ID | Nume Client | Tip Cameră | Nopți | Preț Total\n";
    cout << "---------------------------------------------------\n";
    
    for (int i = 0; i < numarRezervari; i++) {
        cout << rezervari[i].id << " | " 
             << rezervari[i].numeClient << " | " 
             << rezervari[i].tipCamera << " | " 
             << rezervari[i].nopti << " nopți | " 
             << rezervari[i].pretTotal << " lei\n";
    }
}

void cautaRezervareDupaID(const Rezervare* rezervari, int numarRezervari) {
    int idCautat;
    cout << "ID căutat: ";
    cin >> idCautat;
    
    bool gasit = false;
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].id == idCautat) {
            cout << "\n===== DETALII REZERVARE =====\n";
            cout << "ID: " << rezervari[i].id << "\n";
            cout << "Nume client: " << rezervari[i].numeClient << "\n";
            cout << "Tip cameră: " << rezervari[i].tipCamera << "\n";
            cout << "Număr nopți: " << rezervari[i].nopti << "\n";
            cout << "Preț total: " << rezervari[i].pretTotal << " lei\n";
            gasit = true;
            break;
        }
    }
    
    if (!gasit) {
        cout << "Rezervare inexistentă!\n";
    }
}

void stergeRezervare(Rezervare* rezervari, int& numarRezervari) {
    int idSters;
    cout << "ID rezervare de șters: ";
    cin >> idSters;
    
    int indexSters = -1;
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].id == idSters) {
            indexSters = i;
            break;
        }
    }
    
    if (indexSters != -1) {
        // Mutăm toate rezervările cu o poziție spre stânga
        for (int i = indexSters; i < numarRezervari - 1; i++) {
            rezervari[i] = rezervari[i + 1];
        }
        numarRezervari--;
        cout << "Rezervare ștearsă cu succes!\n";
    } else {
        cout << "Rezervare inexistentă!\n";
    }
}

void sorteazaDupaPret(Rezervare* rezervari, int numarRezervari) {
    if (numarRezervari <= 1) {
        cout << "Nu există suficiente rezervări pentru sortare!\n";
        return;
    }
    
    for (int i = 0; i < numarRezervari - 1; i++) {
        for (int j = i + 1; j < numarRezervari; j++) {
            if (rezervari[i].pretTotal > rezervari[j].pretTotal) {
                swap(rezervari[i], rezervari[j]);
            }
        }
    }
    cout << "Rezervările au fost sortate după preț!\n";
}

void sorteazaDupaNume(Rezervare* rezervari, int numarRezervari) {
    if (numarRezervari <= 1) {
        cout << "Nu există suficiente rezervări pentru sortare!\n";
        return;
    }
    
    for (int i = 0; i < numarRezervari - 1; i++) {
        for (int j = i + 1; j < numarRezervari; j++) {
            if (strcmp(rezervari[i].numeClient, rezervari[j].numeClient) > 0) {
                swap(rezervari[i], rezervari[j]);
            }
        }
    }
    cout << "Rezervările au fost sortate după numele clientului!\n";
}

void filtreazaRezervariPremium(const Rezervare* rezervari, int numarRezervari) {
    const float PRET_PREMIUM = 500.0; // Considerăm premium rezervările peste 500 lei
    cout << "\n===== REZERVĂRI PREMIUM =====\n";
    
    bool existaPremium = false;
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].pretTotal > PRET_PREMIUM) {
            cout << rezervari[i].id << " | " 
                 << rezervari[i].numeClient << " | " 
                 << rezervari[i].tipCamera << " | " 
                 << rezervari[i].nopti << " nopți | " 
                 << rezervari[i].pretTotal << " lei\n";
            existaPremium = true;
        }
    }
    
    if (!existaPremium) {
        cout << "Nu există rezervări premium!\n";
    }
}

void actualizeazaRezervare(Rezervare* rezervari, int numarRezervari) {
    int idActualizat;
    cout << "ID rezervare de actualizat: ";
    cin >> idActualizat;
    
    int indexActualizat = -1;
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].id == idActualizat) {
            indexActualizat = i;
            break;
        }
    }
    
    if (indexActualizat == -1) {
        cout << "Rezervare inexistentă!\n";
        return;
    }
    
    cout << "\nRezerva găsită. Actualizați doar informațiile dorite:\n";
    
    cout << "Nume client (" << rezervari[indexActualizat].numeClient << "): ";
    cin.ignore();
    cin.getline(rezervari[indexActualizat].numeClient, 50);
    
    cout << "Tip cameră (" << rezervari[indexActualizat].tipCamera << "): ";
    cin.getline(rezervari[indexActualizat].tipCamera, 20);
    
    cout << "Număr de nopți (" << rezervari[indexActualizat].nopti << "): ";
    cin >> rezervari[indexActualizat].nopti;
    
    cout << "Preț total (" << rezervari[indexActualizat].pretTotal << "): ";
    cin >> rezervari[indexActualizat].pretTotal;
    
    if (validareDate(rezervari[indexActualizat].nopti, rezervari[indexActualizat].pretTotal)) {
        cout << "Rezervare actualizată cu succes!\n";
    }
}

float calculeazaVenituriTotale(const Rezervare* rezervari, int numarRezervari) {
    float sumaTotala = 0;
    
    for (int i = 0; i < numarRezervari; i++) {
        sumaTotala += rezervari[i].pretTotal;
    }
    
    return sumaTotala;
}

void salveazaInFisier(const Rezervare* rezervari, int numarRezervari) {
    FILE* fisier = fopen("rezervari.txt", "w");
    
    if (fisier) {
        for (int i = 0; i < numarRezervari; i++) {
            fprintf(fisier, "%d,%s,%s,%d,%.2f\n", 
                    rezervari[i].id, 
                    rezervari[i].numeClient, 
                    rezervari[i].tipCamera, 
                    rezervari[i].nopti, 
                    rezervari[i].pretTotal);
        }
        fclose(fisier);
        cout << "Rezervările au fost salvate în fișier!\n";
    } else {
        cout << "Eroare la deschiderea fișierului pentru scriere!\n";
    }
}

void incarcaDinFisier(Rezervare*& rezervari, int& numarRezervari, int& capacitate) {
    FILE* fisier = fopen("rezervari.txt", "r");
    
    if (fisier) {
        numarRezervari = 0;
        
        while (true) {
            // Verificare și redimensionare dacă e necesar
            redimensioneazaArray(rezervari, capacitate, numarRezervari);
            
            // Încercare citire din fișier
            if (fscanf(fisier, "%d,%49[^,],%19[^,],%d,%f\n", 
                   &rezervari[numarRezervari].id, 
                   rezervari[numarRezervari].numeClient, 
                   rezervari[numarRezervari].tipCamera, 
                   &rezervari[numarRezervari].nopti, 
                   &rezervari[numarRezervari].pretTotal) != 5) {
                break;
            }
            
            numarRezervari++;
        }
        
        fclose(fisier);
        cout << "S-au încărcat " << numarRezervari << " rezervări din fișier!\n";
    } else {
        cout << "Eroare la deschiderea fișierului pentru citire!\n";
    }
}
