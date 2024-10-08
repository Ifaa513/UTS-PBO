# CRUD Pendataan Mata Kuliah dengan Java Swing 📚
Program ini merupakan implementasi operasi CRUD (Create, Read, Update, dan Delete) dalam sebuah interface pengguna grafis (GUI). Setiap data yang telah berhasil diinputkan akan disimpan dalam sebuah database di PostreSql dan ditampilkan dalam sebuah tabel GUI.
## Langkah-langkah Pembuatan Program ⚡
### 1. 💻 Membuat database di PostgreSQL untuk entitas Mata Kuliah dengan atribut Kode MK, SKS, Nama MK, dan Semester Ajar
    CREATE TABLE MataKuliah (
    KodeMK CHAR(6) PRIMARY KEY,
    NamaMK VARCHAR(100) NOT NULL,
    SKS INTEGER NOT NULL,
    SemesterAjar INTEGER NOT NULL
    );
### 2. 📂 Membuat project baru di Netbeans
### 3. 🖍️ Buat JFrameForm
Berikut rincian properties yang digunakan:
1. 🏷️ Gunakan label untuk judul tabel (Mata Kuliah), Kode MK, Nama MK, SKS, dan Semester Ajar
📝 Gunakan text field untuk field data Kode MK, Nama MK, SKS, dan Semester Ajar
🔘 Gunakan button untuk tombol INSERT, UPDATE, DELETE, dan CLEAR
📊 Gunakan table untuk tampilan Mata Kuliah
### 4. 🔗 Koneksikan dengan database

    Connection conn;
    Statement stmt;
    PreparedStatement pstmt;

    String driver = "org.postgresql.Driver";
    String koneksi = "jdbc:postgresql://localhost:5432/UTSPBO";
    String user = "postgres";
    String password = "5130";
    private BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
### 5. 📊 Buat method untuk menampilkan data buku dalam bentuk tabel

        public void tampil() {
        // TODO code application login
        try (Connection conn = DriverManager.getConnection(koneksi, user, password); Statement stmt = conn.createStatement(); ResultSet rs = stmt.executeQuery("SELECT * FROM MataKuliah ORDER BY KodeMK")) {

            // Dapatkan model tabel yang ada
            DefaultTableModel model = (DefaultTableModel) tblData.getModel();

            // Kosongkan data tabel terlebih dahulu
            model.setRowCount(0);

            // Tambahkan data dari ResultSet ke model tabel
            while (rs.next()) {
                Object[] rowData = {rs.getString(1), rs.getString(2), rs.getString(3), rs.getString(4)};
                model.addRow(rowData);
            }
        } catch (SQLException ex) {
            // Tangani eksepsi
            System.err.println("Terjadi kesalahan: " + ex.getMessage());
        }
        }
### 6. 📥 Action Performed untuk tombol Insert

Fungsi ini berfungsi agar saat pengguna menekan tombol INSERT, data mata kuliah yang dimasukkan pada form akan disimpan ke dalam database dengan menggunakan perintah INSERT INTO. Data ini dimasukkan ke dalam tabel Buku di PostgreSQL.

        private void btnInsertActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String KodeMK, NamaMK, SKS, SemesterAjar;
        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);
            conn.setAutoCommit(false);

            String sql = "INSERT INTO MataKuliah (KodeMK, NamaMK, SKS, SemesterAjar) VALUES(?, ?, ?, ?)";
            pstmt = conn.prepareStatement(sql);

            KodeMK = txtKode.getText();
            NamaMK = txtNama.getText();
            SKS = txtSKS.getText();
            SemesterAjar = txtSem.getText();

            pstmt.setInt(1, Integer.parseInt(KodeMK));
            pstmt.setString(2, NamaMK);
            pstmt.setInt(3, Integer.parseInt(SKS));
            pstmt.setInt(4, Integer.parseInt(SemesterAjar));

            pstmt.executeUpdate(); // executeUpdate hanya dipanggil sekali
            conn.commit();

            JOptionPane.showMessageDialog(null, "Data berhasil disimpan");
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
       }  
 ### 7. 🔄 Action Performed untuk tombol Update

 Fungsi ini berguna agar saat pengguna memilih data Mata Kuliah di tabel dan menekan tombol UPDATE, data Mata Kuliah yang sudah dimodifikasi pada form akan diperbarui di database dengan menggunakan perintah UPDATE.
 
        private void btnUpdateActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String KodeMK = txtKode.getText();
        String NamaMK = txtNama.getText();
        String SKS = txtSKS.getText();
        String SemesterAjar = txtSem.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            String updateSql = "UPDATE MataKuliah SET NamaMK = ?, SKS = ?, SemesterAjar = ? WHERE KodeMK = ?";
            pstmt = conn.prepareStatement(updateSql);

            pstmt.setString(1, NamaMK);
            pstmt.setInt(2, Integer.parseInt(SKS));
            pstmt.setInt(3, Integer.parseInt(SemesterAjar));
            pstmt.setString(4, KodeMK);

            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                JOptionPane.showMessageDialog(null, "Data berhasil diupdate");
            } else {
                JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
        } 
  ### 8. 🗑️ Action Performed untuk tombol Delete

  Fungsi ini dibuat agar pengguna dapat menghapus data mata kuliah berdasarkan KodeMK. Perintah DELETE FROM digunakan untuk menghapus data dari database.

        private void btnDeleteActionPerformed(java.awt.event.ActionEvent evt) {                                          
        String KodeMK = txtKode.getText();

        try {
            Class.forName(driver);
            conn = DriverManager.getConnection(koneksi, user, password);

            String deleteSql = "DELETE FROM MataKuliah WHERE KodeMK = ?";
            pstmt = conn.prepareStatement(deleteSql);

            pstmt.setString(1, KodeMK);

            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                JOptionPane.showMessageDialog(null, "Data berhasil dihapus");
            } else {
                JOptionPane.showMessageDialog(null, "Data tidak ditemukan");
            }
        } catch (ClassNotFoundException | SQLException ex) {
            JOptionPane.showMessageDialog(null, "Terjadi kesalahan: " + ex.getMessage());
        } finally {
            try {
                if (pstmt != null) {
                    pstmt.close();
                }
                if (conn != null) {
                    conn.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        tampil();
        } 
  ### 9. 🧹 Action Performed untuk tombol Clear

  Fungsi ini bekerja dengan mengosongkan semua input pada form, seperti menghapus teks di field Kode MK, Nama MK, SKS, dan Semester Ajar. Tujuannya adalah untuk mereset form agar siap digunakan untuk memasukkan data baru atau menghindari kesalahan input ketika form masih berisi data sebelumnya.

        private void btnClearActionPerformed(java.awt.event.ActionEvent evt) {                                         
        txtKode.setText("");
        txtNama.setText("");
        txtSKS.setText("");
        txtSem.setText("");
        }
  ### 10. 🖱️ tblDataMouseClicked

  Fungsi ini dibuat untuk memudahkan pengguna agar tidak perlu mengisi ulang data mata kuliah yang ia pilih pada baris tabel untuk di update/di delete atau sekedar dilihat saja. Fungsi ini mengambil data dari baris yang diklik oleh pengguna dan menampilkannya ke dalam form input (Kode MK, Nama MK, SKS, dan Semester Ajar)

        private void tblDataMouseClicked(java.awt.event.MouseEvent evt) {                                     
        int row = tblData.getSelectedRow();
        txtKode.setText(tblData.getValueAt(row, 0).toString());
        txtNama.setText(tblData.getValueAt(row, 1).toString());
        txtSKS.setText(tblData.getValueAt(row, 2).toString());
        txtSem.setText(tblData.getValueAt(row, 3).toString());    
        }
  ### 11. 🌟 [DbUtils]()
  Program ini adalah sebuah utility class bernama DbUtils yang menyediakan metode untuk mengubah hasil query dari database berupa ResultSet menjadi objek TableModel. TableModel digunakan untuk menampilkan data dalam komponen GUI seperti JTable di Java Swing.

