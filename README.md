# LabMobile5_EndiniNurlaily_ShiftC

# 1. Proses registrasi
a) Code form Penjelasan + Screenshoot
Berikut untuk soursode dan penjelasannya:
```dart
import 'package:flutter/material.dart';
import 'package:tokokita/bloc/registrasi_bloc.dart';
import 'package:tokokita/widget/success_dialog.dart';
import 'package:tokokita/widget/warning_dialog.dart';

class RegistrasiPage extends StatefulWidget {
  const RegistrasiPage({Key? key}) : super(key: key);

  @override
  _RegistrasiPageState createState() => _RegistrasiPageState();
}

class _RegistrasiPageState extends State<RegistrasiPage> {
  final _formKey = GlobalKey<FormState>();
  bool _isLoading = false;

  final _namaTextboxController = TextEditingController();
  final _emailTextboxController = TextEditingController();
  final _passwordTextboxController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Registrasi"),
      ),
      body: SingleChildScrollView(
        child: Padding(
          padding: const EdgeInsets.all(8.0),
          child: Form(
            key: _formKey,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                _namaTextField(),
                _emailTextField(),
                _passwordTextField(),
                _passwordKonfirmasiTextField(),
                _buttonRegistrasi()
              ],
            ),
          ),
        ),
      ),
    );
  }

  // Membuat Textbox Nama
  Widget _namaTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Nama"),
      keyboardType: TextInputType.text,
      controller: _namaTextboxController,
      validator: (value) {
        if (value!.length < 3) {
          return "Nama harus diisi minimal 3 karakter";
        }
        return null;
      },
    );
  }

  // Membuat Textbox Email
  Widget _emailTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Email"),
      keyboardType: TextInputType.emailAddress,
      controller: _emailTextboxController,
      validator: (value) {
        if (value!.isEmpty) {
          return 'Email harus diisi';
        }
        return null;
      },
    );
  }

  // Membuat Textbox Password
  Widget _passwordTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Password"),
      obscureText: true,
      controller: _passwordTextboxController,
      validator: (value) {
        if (value!.length < 6) {
          return "Password harus diisi minimal 6 karakter";
        }
        return null;
      },
    );
  }

  // Membuat Textbox Konfirmasi Password
  Widget _passwordKonfirmasiTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Konfirmasi Password"),
      obscureText: true,
      validator: (value) {
        if (value != _passwordTextboxController.text) {
          return "Konfirmasi Password tidak sama";
        }
        return null;
      },
    );
  }

  // Membuat Tombol Registrasi
  Widget _buttonRegistrasi() {
    return ElevatedButton(
      child: const Text("Registrasi"),
      onPressed: () {
        var validate = _formKey.currentState!.validate();
        if (validate) {
          if (!_isLoading) _submit();
        }
      },
    );
  }

  // Fungsi Submit
  void _submit() {
    _formKey.currentState!.save();
    setState(() {
      _isLoading = true;
    });

    RegistrasiBloc.registrasi(
            nama: _namaTextboxController.text,
            email: _emailTextboxController.text,
            password: _passwordTextboxController.text)
        .then((value) {
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => SuccessDialog(
                description: "Registrasi berhasil, silahkan login",
                okClick: () {
                  Navigator.pop(context);
                },
              ));
    }, onError: (error) {
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => const WarningDialog(
                description: "Registrasi gagal, silahkan coba lagi",
              ));
    });

    setState(() {
      _isLoading = false;
    });
  }
}
```
![Tampilan Registrasi](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/tampilan_register.png)
b.) SS PopUp Berhasil
![Tampilan pop up Registrasi](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/register_berhasil.png)
# 2. Proses Login
a) Code form Penjelasan + Screenshoot
Berikut untuk soursode dan penjelasannya:
```dart
import 'package:flutter/material.dart';
import 'package:tokokita/bloc/login_bloc.dart';
import 'package:tokokita/helpers/user_info.dart';
import 'package:tokokita/ui/produk_page.dart';
import 'package:tokokita/widget/warning_dialog.dart';

class LoginPage extends StatefulWidget {
  const LoginPage({Key? key}) : super(key: key);

  @override
  _LoginPageState createState() => _LoginPageState();
}

class _LoginPageState extends State<LoginPage> {
  final _formKey = GlobalKey<FormState>();
  bool _isLoading = false;

  final _emailTextboxController = TextEditingController();
  final _passwordTextboxController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Login'),
      ),
      body: SingleChildScrollView(
        child: Padding(
          padding: const EdgeInsets.all(8.0),
          child: Form(
            key: _formKey,
            child: Column(
              children: [
                _emailTextField(),
                _passwordTextField(),
                _buttonLogin(),
                const SizedBox(height: 30),
                _menuRegistrasi(),
              ],
            ),
          ),
        ),
      ),
    );
  }

  // Membuat Textbox Email
  Widget _emailTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Email"),
      keyboardType: TextInputType.emailAddress,
      controller: _emailTextboxController,
      validator: (value) {
        if (value!.isEmpty) {
          return 'Email harus diisi';
        }
        return null;
      },
    );
  }

  // Membuat Textbox Password
  Widget _passwordTextField() {
    return TextFormField(
      decoration: const InputDecoration(labelText: "Password"),
      obscureText: true,
      controller: _passwordTextboxController,
      validator: (value) {
        if (value!.isEmpty) {
          return "Password harus diisi";
        }
        return null;
      },
    );
  }

  // Membuat Tombol Login
  Widget _buttonLogin() {
    return ElevatedButton(
      child: const Text("Login"),
      onPressed: () {
        var validate = _formKey.currentState!.validate();
        if (validate) {
          if (!_isLoading) _submit();
        }
      },
    );
  }

  // Fungsi Submit Login
  void _submit() {
    _formKey.currentState!.save();
    setState(() {
      _isLoading = true;
    });

    LoginBloc.login(
            email: _emailTextboxController.text,
            password: _passwordTextboxController.text)
        .then((value) async {
      if (value.code == 200) {
        await UserInfo().setToken(value.token.toString());
        await UserInfo().setUserID(int.parse(value.userID.toString()));
        Navigator.pushReplacement(context,
            MaterialPageRoute(builder: (context) => const ProdukPage()));
      } else {
        showDialog(
            context: context,
            barrierDismissible: false,
            builder: (BuildContext context) => const WarningDialog(
                  description: "Login gagal, silahkan coba lagi",
                ));
      }
    }, onError: (error) {
      showDialog(
          context: context,
          barrierDismissible: false,
          builder: (BuildContext context) => const WarningDialog(
                description: "Login gagal, silahkan coba lagi",
              ));
    });

    setState(() {
      _isLoading = false;
    });
  }

  // Membuat Menu Registrasi
  Widget _menuRegistrasi() {
    return Center(
      child: InkWell(
        child: const Text(
          "Registrasi",
          style: TextStyle(color: Colors.blue),
        ),
        onTap: () {
          Navigator.push(context,
              MaterialPageRoute(builder: (context) => const RegistrasiPage()));
        },
      ),
    );
  }
}
```
![Tampilan Login](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/tampilan_login.png)
b.) SS PopUp Berhasil
![Tampilan pop up Login](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/login_berhasil.png)
# 3. Daftar Produk
a) Code form Penjelasan + Screenshoot
Berikut untuk soursode dan penjelasannya:
```dart
import 'package:flutter/material.dart';
import 'package:tokokita/bloc/produk_bloc.dart';
import 'package:tokokita/ui/detail_produk_page.dart';
import 'package:tokokita/widget/item_produk.dart';

class DaftarProdukPage extends StatefulWidget {
  const DaftarProdukPage({Key? key}) : super(key: key);

  @override
  _DaftarProdukPageState createState() => _DaftarProdukPageState();
}

class _DaftarProdukPageState extends State<DaftarProdukPage> {
  late Future<List<Produk>> futureProduks;

  @override
  void initState() {
    super.initState();
    futureProduks = ProdukBloc.getProduks(); // Mengambil data produk
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Daftar Produk"),
      ),
      body: FutureBuilder<List<Produk>>(
        future: futureProduks,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          } else if (snapshot.hasError) {
            return Center(child: Text('Error: ${snapshot.error}'));
          } else {
            final produkList = snapshot.data ?? [];
            return ListView.builder(
              itemCount: produkList.length,
              itemBuilder: (context, index) {
                return ItemProduk(
                  produk: produkList[index],
                  onTap: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) => DetailProdukPage(produk: produkList[index]),
                      ),
                    );
                  },
                );
              },
            );
          }
        },
      ),
    );
  }
}
```
![Tampilan Daftar Produk](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/tampilan_daftar_produk.png)
b.) SS PopUp Berhasil![Tampilan Daftar Produk Berhasil](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/berhasil_daftar_produk.png)
# 4. Detail Produk
a) Code form Penjelasan + Screenshoot
Berikut untuk soursode dan penjelasannya:
```dart
import 'package:flutter/material.dart';
import 'package:tokokita/model/produk.dart';

class DetailProdukPage extends StatelessWidget {
  final Produk produk;

  const DetailProdukPage({Key? key, required this.produk}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(produk.namaProduk),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              produk.namaProduk,
              style: const TextStyle(fontSize: 24, fontWeight: FontWeight.bold),
            ),
            const SizedBox(height: 10),
            Text("Kode Produk: ${produk.kodeProduk}"),
            const SizedBox(height: 10),
            Text("Harga: \$${produk.hargaProduk}"),
            const SizedBox(height: 10),
            Text("Deskripsi: ${produk.deskripsiProduk}"),
          ],
        ),
      ),
    );
  }
}
```
![Tampilan Detail Produk](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/detail_produk.png)
b.) SS PopUp Berhasil
![Tampilan pop up Detail Produk](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/detail_produl_berhasil.png)
# 5. Konfirmasi Hapus Produk (Delete)
a) Code form Penjelasan + Screenshoot
Berikut untuk soursode dan penjelasannya:
```dart
import 'package:flutter/material.dart';
import 'package:tokokita/bloc/produk_bloc.dart';
import 'package:tokokita/widget/item_produk.dart';

class DaftarProdukPage extends StatefulWidget {
  const DaftarProdukPage({Key? key}) : super(key: key);

  @override
  _DaftarProdukPageState createState() => _DaftarProdukPageState();
}

class _DaftarProdukPageState extends State<DaftarProdukPage> {
  late Future<List<Produk>> futureProduks;

  @override
  void initState() {
    super.initState();
    futureProduks = ProdukBloc.getProduks(); // Mengambil data produk
  }

  void _confirmDelete(Produk produk) {
    showDialog(
      context: context,
      builder: (BuildContext context) {
        return AlertDialog(
          title: Text("Konfirmasi Hapus"),
          content: Text("Apakah Anda yakin ingin menghapus ${produk.namaProduk}?"),
          actions: <Widget>[
            TextButton(
              child: Text("Batal"),
              onPressed: () {
                Navigator.of(context).pop(); // Menutup dialog
              },
            ),
            TextButton(
              child: Text("Hapus"),
              onPressed: () {
                // Panggil fungsi hapus produk
                ProdukBloc.deleteProduk(produk.kodeProduk);
                setState(() {
                  futureProduks = ProdukBloc.getProduks(); // Refresh daftar produk
                });
                Navigator.of(context).pop(); // Menutup dialog
              },
            ),
          ],
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Daftar Produk"),
      ),
      body: FutureBuilder<List<Produk>>(
        future: futureProduks,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.waiting) {
            return const Center(child: CircularProgressIndicator());
          } else if (snapshot.hasError) {
            return Center(child: Text('Error: ${snapshot.error}'));
          } else {
            final produkList = snapshot.data ?? [];
            return ListView.builder(
              itemCount: produkList.length,
              itemBuilder: (context, index) {
                return ItemProduk(
                  produk: produkList[index],
                  onDelete: () => _confirmDelete(produkList[index]), // Opsi hapus
                );
              },
            );
          }
        },
      ),
    );
  }
}
```
![Tampilan Detail Produk](https://raw.githubusercontent.com/endiniii/LabMobile5_EndiniNurlaily_ShiftC/main/detail_produk.png)
