#include <iostream>
#include <vector>
#include <limits> // For std::numeric_limits
#include <string>
using namespace std;

// Struktur Data Menu
struct Menu {
    string nama;
    double harga;
    int ketersediaan; // 1 = tersedia, 0 = habis
};

// Struktur Data Pesanan
struct Pesanan {
    string namaMenu;
    int jumlah;
    double totalHarga;
};

// Vektor untuk menyimpan data Menu dan Pesanan
vector<Menu> daftarMenu;
vector<Pesanan> daftarPesanan;

// Fungsi Menampilkan Daftar Menu
void tampilkanMenu() {
    cout << "\n=== Daftar Menu ===\n";
    for (int i = 0; i < daftarMenu.size(); i++) {
        cout << i + 1 << ". " << daftarMenu[i].nama << " - Rp"
            << daftarMenu[i].harga << " - "
            << (daftarMenu[i].ketersediaan ? "Tersedia" : "Habis") << endl;
    }
}

// Fungsi Input Menu (Owner)
void inputMenu() {
    int jumlah;
    cout << "Masukkan jumlah menu yang ingin ditambahkan: ";
    cin >> jumlah;

    // Validasi input jumlah
    while (cin.fail() || jumlah <= 0) {
        cin.clear(); // Clear the error flag
        cin.ignore(numeric_limits<streamsize>::max(), '\n'); // Ignore invalid input
        cout << "Input tidak valid. Masukkan jumlah menu yang ingin ditambahkan: ";
        cin >> jumlah;
    }

    cin.ignore(); // Clear the newline character from the buffer

    for (int i = 0; i < jumlah; i++) {
        Menu m;
        cout << "Masukkan nama menu ke-" << i + 1 << ": ";
        getline(cin, m.nama); // Use getline to allow spaces in menu names
        cout << "Masukkan harga menu: ";
        cin >> m.harga;

        // Validasi input harga
        while (cin.fail() || m.harga < 0) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Input tidak valid. Masukkan harga menu: ";
            cin >> m.harga;
        }

        m.ketersediaan = 1; // Default tersedia
        daftarMenu.push_back(m);
    }
    cout << "Menu berhasil ditambahkan.\n";
}

// Fungsi Update Ketersediaan Menu (Staff)
void updateKetersediaan() {
    string namaMenu;
    int status;

    cout << "Masukkan nama menu yang akan diperbarui: ";
    cin.ignore();
    getline(cin, namaMenu); // Use getline to allow spaces in menu names

    cout << "Masukkan status ketersediaan (1 = tersedia, 0 = habis): ";
    cin >> status;

    // Validasi input status
    while (cin.fail() || (status != 0 && status != 1)) {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
        cout << "Input tidak valid. Masukkan status ketersediaan (1 = tersedia, 0 = habis): ";
        cin >> status;
    }

    for (Menu& m : daftarMenu) {
        if (m.nama == namaMenu) {
            m.ketersediaan = status;
            cout << "Status menu berhasil diperbarui.\n";
            return;
        }
    }
    cout << "Menu tidak ditemukan.\n";
}

// Fungsi Input Pesanan (Staff)
void inputPesanan() {
    tampilkanMenu(); // Tampilkan menu sebelum memesan

    string namaMenu;
    int jumlah;

    cout << "Masukkan nama menu yang dipesan: ";
    cin.ignore();
    getline(cin, namaMenu); // Use getline to allow spaces in menu names

    cout << "Masukkan jumlah pesanan: ";
    cin >> jumlah;

    // Validasi input jumlah
    while (cin.fail() || jumlah <= 0) {
        cin.clear();
        cin.ignore(numeric_limits<streamsize>::max(), '\n');
        cout << "Input tidak valid. Masukkan jumlah pesanan: ";
        cin >> jumlah;
    }

    for (Menu& m : daftarMenu) {
        if (m.nama == namaMenu && m.ketersediaan == 1) {
            Pesanan p;
            p.namaMenu = namaMenu;
            p.jumlah = jumlah;
            p.totalHarga = m.harga * jumlah;
            daftarPesanan.push_back(p);
            cout << "Pesanan berhasil dicatat.\n";
            return;
        }
    }
    cout << "Menu tidak tersedia atau habis.\n";
}

// Fungsi Hitung Total Tagihan (Staff)
void hitungTotalTagihan() {
    double total = 0;

    if (daftarPesanan.empty()) {
        cout << "\nTidak ada pesanan untuk ditampilkan.\n";
        return;
    }

    cout << "\n=== Pesanan Pelanggan ===\n";

    for (Pesanan p : daftarPesanan) {
        cout << p.namaMenu << " - Jumlah: " << p.jumlah
            << " - Total Harga: Rp" << p.totalHarga << endl;
        total += p.totalHarga;
    }

    cout << "Total Tagihan: Rp" << total << endl;
}

// Menu Utama Program
void menuUtama() {
    int pilihan;

    do {
        cout << "\n===== Warteg POS System =====\n";
        cout << "1. Input Menu (Owner)\n";
        cout << "2. Update Ketersediaan Menu (Staff)\n";
        cout << "3. Input Pesanan (Staff)\n";
        cout << "4. Hitung Total Tagihan (Staff)\n";
        cout << "5. Tampilkan Menu\n";
        cout << "6. Keluar\n";

        cout << "Pilih menu: ";
        cin >> pilihan;

        // Validasi input pilihan
        while (cin.fail() || pilihan < 1 || pilihan > 6) {
            cin.clear();
            cin.ignore(numeric_limits<streamsize>::max(), '\n');
            cout << "Pilihan tidak valid. Silakan coba lagi: ";
            cin >> pilihan;
        }

        switch (pilihan) {
        case 1: inputMenu(); break;
        case 2: updateKetersediaan(); break;
        case 3: inputPesanan(); break;
        case 4: hitungTotalTagihan(); break;
        case 5: tampilkanMenu(); break;
        case 6:
            cout << "Terima kasih telah menggunakan Warteg POS System!\n";
            break;
        default:
            cout << "Pilihan tidak valid. Silakan coba lagi.\n";
            break;
        }

    } while (pilihan != 6);
}

// Fungsi Utama
int main() {
    menuUtama();
    return 0;
}
