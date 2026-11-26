# VaxiTrace

A student web project for COVID-19 vaccination registration in Mumbai, Pune and Nagpur, written in plain PHP with a
MySQL database. The pages call the application COVtracker.

It is a local prototype and must not be deployed or given real personal data. See [Known problems](#known-problems).

## Features

- Account sign-up and login with PHP sessions; passwords are stored with `password_hash`.
- A registration form that stores name, Aadhaar number, address, age and related fields.
- A form for recording administered vaccinations (`admin.php`).
- Charts of vaccinations by age group (Google Charts) and by city (Morris.js), read from summary tables.
- Static lists of hospitals for the three cities, with a "Book slot" button that only shows a success page.
- FAQ and About pages.

There is no automatic aggregation, slot management, inventory or warehouse tracking. The chart tables have to be filled
by hand.

## Layout

| Path | Contents |
|---|---|
| `index.php` | Landing page |
| `register.php`, `login.php`, `logout.php`, `config.php` | Accounts |
| `welcome.php` | Signed-in home page with links and 2021 news items |
| `registration.php` | Vaccination registration form |
| `admin.php` | Vaccination record form |
| `graphs.php` | Charts |
| `mumbai.html`, `pune.html`, `nagpur.html`, `regSuccess.html` | Hospital lists and booking confirmation |
| `faq.html`, `aboutUs.html` | FAQ and team page |
| `assets/` | Stylesheets and FAQ page scripts |

## Running locally

You need PHP with the `mysqli` extension and MySQL or MariaDB, for example through XAMPP. The pages connect to
`localhost` as `root` with an empty password.

The code refers to the database as both `covtracker` and `COVtracker`. On Linux, start the server with
`lower_case_table_names=1` (set when the data directory is initialised on MySQL), or make the names match.

No schema file was ever committed. The following was reconstructed from the queries; column types are a choice made for
this README, but the column order matters because the inserts do not name columns.

```sql
CREATE DATABASE covtracker;
USE covtracker;
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) NOT NULL UNIQUE,
  password VARCHAR(255) NOT NULL
);
CREATE TABLE registration (
  first_name VARCHAR(100), last_name VARCHAR(100), aadhaar VARCHAR(20), address VARCHAR(255),
  city VARCHAR(100), gender VARCHAR(20), age VARCHAR(10), dob VARCHAR(20), email VARCHAR(255)
);
CREATE TABLE vaccination (
  first_name VARCHAR(100), last_name VARCHAR(100), aadhaar VARCHAR(20), age VARCHAR(10), gender VARCHAR(20),
  vaccination_date VARCHAR(20), vaccine VARCHAR(100), city VARCHAR(100), hospital VARCHAR(255)
);
CREATE TABLE age (category VARCHAR(50), number_vaccinated INT);
CREATE TABLE cityy (city VARCHAR(100), number_vaccinated INT);
```

Then, from the repository root:

```bash
php -S localhost:8000
```

Open <http://localhost:8000/index.php>. After login, a username that starts with a digit goes to `welcome.php`; any
other username goes to `admin.php`.

## Known problems

Security:

- `admin.php` has no login check, and anyone who registers a username starting with a letter is sent to it.
- `registration.php` and `admin.php` build SQL by inserting form values into the query string (SQL injection).
- `login.php` and `register.php` echo submitted values back without escaping (cross-site scripting).
- Aadhaar numbers and other personal data are stored in plain text, with no CSRF protection.
- The database account is `root` with no password.

Behaviour:

- Opening `registration.php` or `admin.php` without submitting the form inserts an empty row.
- The password reset link on `welcome.php` points to a page that does not exist, and the header link on
  `registration.php` points to a missing `index.html`.
- The FAQ describes features that are not implemented, such as password reset and certificate download.
- `assets/css/faq.scss` cannot be compiled here because it imports the CodyHouse framework, which is not included. The
  committed `faq.css` is what the page uses.
- Images, fonts and libraries are loaded from third-party sites, so some may no longer load.

There are no automated tests.

## Credits

Team listed on the About page: Sushil Dalavi, Aadesh Sawant, Sarvesh Patil and Tanvesh Nivelkar.

The FAQ page uses the FAQ template by [CodyHouse](https://codyhouse.co/), which keeps its own licence terms.

## License

An earlier version of this README stated that the project is MIT licensed. No licence file is included.
