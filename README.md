#include <iostream> 
#include <cstring> 

using namespace std;

struct Rezervare {
    int ID;
    char Client[50];
    char Camera[20];
    int Nopti;
    float Pret;
};

void AdaugaRezervare(Rezervare*& rezervari, int& numarRezervari, int& capacitate) {
    if (numarRezervari >= capacitate) {
        capacitate *= 2;
        Rezervare* temp = new Rezervare[capacitate];
        for (int i = 0; i < numarRezervari; i++) {
            temp[i] = rezervari[i];
        }
        delete[] rezervari;
        rezervari = temp;
    }

    cout << "ID: ";
    cin >> rezervari[numarRezervari].ID;
    cout << "Nume client: ";
    cin.ignore();
    cin.getline(rezervari[numarRezervari].Client, 50);
    cout << "Tip cameră: ";
    cin.getline(rezervari[numarRezervari].Camera, 20);
    cout << "Număr de nopți: ";
    cin >> rezervari[numarRezervari].Nopti;
    if (rezervari[numarRezervari].Nopti <= 0) {
        exit (0);
    }
    cout << "Preț total: ";
    cin >> rezervari[numarRezervari].Pret;
    if (rezervari[numarRezervari].Pret <= 0) {
        exit (0);
    } 

    numarRezervari++;
}

void AfisareRezervare(Rezervare*& rezervari, int& numarRezervari) {
    for (int i = 0; i < numarRezervari; i++) {
                cout << rezervari[i].ID << " | " << rezervari[i].Client << " | " << rezervari[i].Camera << " | " << rezervari[i].Nopti << " nopți | " << rezervari[i].Pret << " lei\n";
            }
}

void CautareID(Rezervare*& rezervari, int& numarRezervari) {
    int idCautat;
            cout << "ID căutat: ";
            cin >> idCautat;
            bool gasit = false;
            for (int i = 0; i < numarRezervari; i++) {
                if (rezervari[i].ID == idCautat) {
                    cout << "Rezervare găsită: " << rezervari[i].Client << " - " << rezervari[i].Camera << " (" << rezervari[i].Nopti << " nopți, " << rezervari[i].Pret << " lei)\n";
                    gasit = true;
                    break;
                }
            }
            if (!gasit) cout << "Rezervare inexistentă!\n";
}

void StergeRezervare(Rezervare*& rezervari, int& numarRezervari) {
    int idSters;
            cout << "ID rezervare de șters: ";
            cin >> idSters;
            int indexSters = -1;
            for (int i = 0; i < numarRezervari; i++) {
                if (rezervari[i].ID == idSters) {
                    indexSters = i;
                    break;
                }
            }
            if (indexSters != -1) {
                for (int i = indexSters; i < numarRezervari - 1; i++) {
                    rezervari[i] = rezervari[i + 1];
                }
                numarRezervari--;
                cout << "Rezervare ștearsă!\n";
            }
            else {
                cout << "Rezervare inexistentă!\n";
            }
}

void SortPret(Rezervare*& rezervari, int& numarRezervari) {
    for (int i = 0; i < numarRezervari - 1; i++) {
                for (int j = i + 1; j < numarRezervari; j++) {
                    if (rezervari[i].Pret > rezervari[j].Pret) {
                        swap(rezervari[i], rezervari[j]);
                    }
                }
            }
            cout << "Sortare realizată!\n";
}

void SortNume(Rezervare*& rezervari, int& numarRezervari) {
    for (int i = 0; i < numarRezervari - 1; i++) {
                for (int j = i + 1; j < numarRezervari; j++) {
                    if (rezervari[i].Client > rezervari[j].Client) {
                        swap(rezervari[i], rezervari[j]);
                    }
                }
            }
            cout << "Sortare realizată!\n";
}

void Salvare(Rezervare*& rezervari, int& numarRezervari) {
    FILE* fisier = fopen("rezervari.txt", "w");
            if (fisier) {
                for (int i = 0; i < numarRezervari; i++) {
                    fprintf(fisier, "%d,%s,%s,%d,%.2f\n", rezervari[i].ID, rezervari[i].Client, rezervari[i].Camera, rezervari[i].Nopti, rezervari[i].Pret);
                }
                fclose(fisier);
                cout << "Rezervările au fost salvate în fișier.\n";
            }
            else {
                cout << "Eroare la deschiderea fișierului!\n";
            }
}

void IncarcareDinFisier(Rezervare*& rezervari, int& numarRezervari, int& capacitate) {
    FILE* fisier = fopen("rezervari.txt", "r");
            if (fisier) {
                numarRezervari = 0;
                while (fscanf(fisier, "%d,%49[^,],%19[^,],%d,%f\n", &rezervari[numarRezervari].ID, rezervari[numarRezervari].Client, rezervari[numarRezervari].Camera, &rezervari[numarRezervari].Nopti, &rezervari[numarRezervari].Pret) == 5) {
                    numarRezervari++;
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
                fclose(fisier);
                cout << "Rezervările au fost încărcate din fișier.\n";
            }
            else {
                cout << "Eroare la deschiderea fișierului!\n";
            }
}

void FiltrareDupaPrimaLitera(Rezervare*& rezervari, int& numarRezervari) {
    char litera;
    cout << "Introduceți litera cu care să înceapă numele clientului: ";
    cin >> litera;
    bool gasit = false;

    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].Client[0] == litera) {
            cout << rezervari[i].ID << " | " << rezervari[i].Client << " | " << rezervari[i].Camera << " | " << rezervari[i].Nopti << " nopți | " << rezervari[i].Pret << " lei\n";
            gasit = true;
        }
    }
    if (!gasit) {
        cout << "Nu există rezervări cu numele care începe cu litera " << litera << "!\n";
    }
}

void actualizeazaRezervare(Rezervare* rezervari, int numarRezervari, int ID, const char Camera[], int Nopti, float Pret) {
    for (int i = 0; i < numarRezervari; i++) {
        if (rezervari[i].ID == ID) {
            strncpy(rezervari[i].Camera, Camera, sizeof(rezervari[i].Camera) - 1);
            rezervari[i].Camera[sizeof(rezervari[i].Camera) - 1] = '\0'; 
            rezervari[i].Nopti = Nopti;
            rezervari[i].Pret = Pret;
            cout << "Rezervare actualizată cu succes!\n";
            return;
        }
    }
    cout << "Rezervare cu ID-ul " << ID << " nu a fost găsită.\n";
}

void CalculareVenituri(Rezervare* rezervari, int numarRezervari) {
    float venitTotal = 0;
    for (int i = 0; i < numarRezervari; i++) {
        venitTotal += rezervari[i].Pret;
    }
    cout << "Venitul total generat este: " << venitTotal << " lei\n";
}

int main() {
    int capacitate = 10, numarRezervari = 0;
    Rezervare* rezervari = new Rezervare[capacitate];

    while (true) {
        cout << "\n1. Adaugă rezervare";
        cout << "\n2. Afișează rezervările";
        cout << "\n3. Caută rezervare după ID";
        cout << "\n4. Șterge rezervare";
        cout << "\n5. Sortează";
        cout << "\n6. Salvează în fișier";
        cout << "\n7. Încarcă din fișier";
        cout << "\n8. Filtru dupa litera";
        cout << "\n9. Actualizeaza rezervare";
        cout << "\n10. Veniturile generale";
        cout << "\n0. Ieșire";
        cout << "\nAlege o opțiune: ";

        int optiune;
        cin >> optiune;

        if (optiune == 1) { // Adăugare rezervare 
        AdaugaRezervare(rezervari, numarRezervari, capacitate);
        }

        else if (optiune == 2) { // Afișare rezervări 
        AfisareRezervare(rezervari, numarRezervari);
        }

        else if (optiune == 3) { // Căutare după ID 
        CautareID(rezervari, numarRezervari);
        }

        else if (optiune == 4) { // Ștergere rezervare 
        StergeRezervare(rezervari, numarRezervari);
        }

        else if (optiune == 5) { // Sortare după preț 
        int varianta;
        cout << "Cum dorești să sortezi înainte de salvare?" << endl;
        cout << "1 - Sortează după preț" << endl;
        cout << "2 - Sortează după nume" << endl;
        cout << "Introdu varianta: ";
        cin >> varianta;

        if (varianta == 1) {
                SortPret(rezervari, numarRezervari);
                Salvare(rezervari, numarRezervari);
        } else if (varianta == 2) {
                SortNume(rezervari, numarRezervari);
                Salvare(rezervari, numarRezervari);
            }
        }
        else if (optiune == 6) { // Salvare în fișier
        Salvare(rezervari, numarRezervari);
        }
        else if (optiune == 7) { // Încărcare din fișier 
        IncarcareDinFisier(rezervari, numarRezervari, capacitate);
        }
        else if (optiune == 8) { // Filtru
        FiltrareDupaPrimaLitera(rezervari, numarRezervari);
        }
        else if (optiune == 9) { // Filtru
        int ID;
        char Camera[20];
        int Nopti;
        float Pret;

        cout << "Introduceti ID-ul rezervarii de actualizat: ";
        cin >> ID;
        cout << "Introduceti camera noua: ";
        cin.ignore();  
        cin.getline(Camera, 20);
        cout << "Introduceti numarul de nopti: ";
        cin >> Nopti;
        if (Nopti <= 0) {
            exit (0);
        }
        cout << "Introduceti pretul: ";
        cin >> Pret;
        if (Pret <= 0) {
            exit (0);
        }

        actualizeazaRezervare(rezervari, numarRezervari, ID, Camera, Nopti, Pret);
    }
        else if (optiune == 10) {
        CalculareVenituri(rezervari, numarRezervari);
        }
        else if (optiune == 0) { // Ieșire 
            break;
        }
        else {
            cout << "Opțiune invalidă!\n";
        }
    }

    delete[] rezervari;
    return 0;
}
