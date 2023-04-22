# SQL Sorgu Alıştırmaları

Bu hafta SQL sorguları üzerine çalışıyorsunuz. Bugünkü alıştırmada sizin için hazırladığımız veritabanında aşağıda istediğimiz sonuçları elde etmenize yarayan SQL sorgularını oluşturacaksınız.

# Proje Kurulumu

Projeyi forklayın ve clonlayın. Tamamladığınızda da pushlayın.

## Kütüphane Bilgi Sistemi

Bu veritabanı, bir okulun kütüphanesinden öğrencilerin aldıkları kitapların bilgisini barındırmaktadır.

#Tablolar
`ogrenci` tablosu öğrencilerin listesini tutar.
`islem` tablosu öğrencilerin kütüphaneden aldıkları kitapların bilgilerini tutar
`kitap` tablosu kütüphanedeki kitapların bilgisini tutar
`yazar` tablosu kitapların yazarları bilgisini tutar
`tur` tablosu kitapların türlerini tutar.

Tablo ilişiklerini görmek için [ktphn.png] dosyasına göz atın.

Yazdığınız sorguları buradan test edebilirsiniz: [https://ergineer.com/assets/materials/fkg36so5-kutuphanebilgisistemi-sql/]

islem --> islemno ogrno kitapno atarih vtarih

kitap --> kitapno isbnno kitapadi yazarno turno sayfasayisi puan

ogrenci --> ogrno ograd ogrsoyad cinsiyet dtarih sinif puan

tur --> turno turadi

yazar --> yazarno yazarad yazarsoyad

# Görevler

Aşağıda istenilen sonuçlara ulaşabilmek için gerekli SQL sorgularını yazın.

    1) ÖRNEK SORU: Yazar tablosunu KEMAL UYUMAZ isimli yazarı ekleyin.

    INSERT INTO yazar (yazarad,yazarsoyad) VALUES ("Kemal","Uyumaz");

    2) Biyografi türünü tür tablosuna ekleyiniz.

    INSERT INTO tur (turadi) VALUES ("Biyografi");

    3) 10A sınıfı olan ÇAĞLAR ÜZÜMCÜ isimli erkek, sınıfı 9B olan LEYLA ALAGÖZ isimli kız ve sınıfı 11C olan Ayşe Bektaş isimli kız öğrencileri tek sorguda ekleyin.

     INSERT INTO  ogrenci (sinif,ograd,ogrsoyad,cinsiyet) VALUES
     		("10A","Çağlar","Üzümcü","E"),
    		("9B","Leyla","Alagöz","K"),
    		("11C","Ayşe","Bektaş","K");

    4) Öğrenci tablosundaki rastgele bir öğrenciyi yazarlar tablosuna yazar olarak ekleyiniz.

    INSERT INTO yazar (yazarad,yazarsoyad) SELECT ograd,ogrsoyad FROM ogrenci ORDER BY RAND() LIMIT 1; -->VALUE ile çalışmadı

    SELECT @@IDENTITY; -->tablodaki son id yi getiriyor

    5) Öğrenci numarası 10 ile 30 arasındaki öğrencileri yazar olarak ekleyiniz.

    INSERT INTO yazar (yazarad,yazarsoyad) (SELECT ograd,ogrsoyad FROM ogrenci WHERE ogrno BETWEEN 10 AND 30);

    6) Nurettin Belek isimli yazarı ekleyip yazar numarasını yazdırınız.
    (Not: Otomatik arttırmada son arttırılan değer @@IDENTITY değişkeni içinde tutulur.)

    INSERT INTO yazar (yazarad,yazarsoyad) VALUES ("Nurettin","Belek")

    SELECT @@IDENTITY; -->tablodaki son id yi getiriyor

    7) 10B sınıfındaki öğrenci numarası 3 olan öğrenciyi 10C sınıfına geçirin.

    UPDATE ogrenci SET sinif='10C' WHERE ogrno='3'

    7.2)10C sınıfındaki en yaşlı iki öğrencinin puanına 5 puan ekle

    UPDATE ogrenci SET puan=puan+5 WHERE sinif="10C" ORDER BY dtarih asc LIMIT 2

    8) 9A sınıfındaki tüm öğrencileri 10A sınıfına aktarın

    UPDATE ogrenci SET sinif="10A" WHERE sinif="9A"

    8.1)10A sınıfındaki en genç beş öğrencinin soyadının yanına cinsiyetilerini yaz

    UPDATE ogrenci SET ogrsoyad=CONCAT(TRIM(ogrsoyad), "(", cinsiyet , ")") WHERE sinif="10A" ORDER BY dtarih DESC LIMIT 5

    9) Tüm öğrencilerin puanını 5 puan arttırın.

    UPDATE ogrenci SET puan=puan+5

    10) 25 numaralı yazarı silin.

    DELETE yazar WHERE yazarno="25"

    11) Doğum tarihi null olan öğrencileri listeleyin. (insert sorgusu ile girilen 3 öğrenci listelenecektir)

     SELECT * FROM ogrenci WHERE dtarih IS NULL

    12) Doğum tarihi null olan öğrencileri silin.

    DELETE yazar WHERE dtarih IS NULL

    13) Kitap tablosunda adı a ile başlayan kitapların puanlarını 2 artırın.

    UPDATE kitap SET puan=puan+2 WHERE kitapadi LIKE "A%"

    13.2) 200 sayfadan fazla okuyan öğrencilere puan ekle

    UPDATE ogrenci SET puan=puan+15 ,ograd=CONCAT(ograd, "*****")
    WHERE ogrno IN(
    	SELECT ogrenci.ogrno FROM ogrenci
    	INNER JOIN islem ON ogrenci.ogrno=islem.ogrno
    	INNER JOIN kitap ON kitap.kitapno=islem.kitapno
    	ORDER BY SUM(kitapno.sayfasayisi) DESC
    	GROUP BY ogrenci.ogrno
    	LIMIT 5
    	)


    14) Kişisel Gelişim isimli bir tür oluşturun.

    INSERT INTO tur (turadi) VALUES ("Kisisel Gelisim");

    15) Kitap tablosundaki Başarı Rehberi kitabının türünü bu tür ile değiştirin.

    UPDATE kitap SET tur="Kisisel Gelisim" WHERE kitapadi="Başarı Rehberi"

    16) Öğrenci tablosunu kontrol etmek amaçlı tüm öğrencileri görüntüleyen "ogrencilistesi" adında bir prosedür oluşturun.


    17) Öğrenci tablosuna yeni öğrenci eklemek için "ekle" adında bir prosedür oluşturun.

    CREATE PROCEDURE Ekle(
    	IN name VARCHAR(50),
    	IN surname VARCHAR(50)
    	)
    BEGIN
    	INSERT INTO ogrenci(ograd, ogrsoyad)
    	VALUES (name, surname);
    END;
    CALL Ekle('Irem','Celebi')
    SELECT * FROM ogrenci WHERE ograd="Irem" AND ogrsoyad="Celebi"

    18) Öğrenci noya göre öğrenci silebilmeyi sağlayan "sil" adında bir prosedür oluşturun.


    19) Öğrenci numarasını kullanarak kolay bir biçimde öğrencinin sınıfını değiştirebileceğimiz bir prosedür oluşturun.


    20) Öğrenci adı ve soyadını "Ad Soyad" olarak birleştirip, ad soyada göre kolayca arama yapmayı sağlayan bir prosedür yazın.


    21) Daha önceden oluşturduğunu tüm prosedürleri silin.


    #Esnek görevler (Esnek görevlerin hepsini Select in Select ile gerçekleştirmeniz beklenmektedir.)
    22) Select in select yöntemiyle dram türündeki kitapları listeleyiniz.


    23) Adı e harfi ile başlayan yazarların kitaplarını listeleyin.


    24) Kitap okumayan öğrencileri listeleyiniz.


    25) Okunmayan kitapları listeleyiniz


    26) Mayıs ayında okunmayan kitapları listeleyiniz.
