---
  title: Ruby’de Metot Arama ve Yürütme Hiyerarşisi
  description: Her sorunun bir cevabı olur, o halde her cevabın da bir sorusu olmalıdır. Bu makalenin sorusu da; Ruby’de bir nesne üzerinde bir metot kullanırken, Ruby getirilecek metodu nasıl bulur?
  date: 2024-07-08 11:07 +0300
  categories: [Software]
  tags: [Ruby, Ruby on Rails, Method Lookup Path]
  image:
    path: assets/img/method_lookup_path/method-lookup-path.jpeg
    alt: Created by Samet Polat with Microsoft Designer
---

**Sınıflar**, nesneler oluşturmak için planlardır. Sınıftan oluşturulan nesnelerin sahip olacağı özellikleri ve davranışları (yani metotları) tanımlarlar. Sınıflar **tekli kalıtımı** destekler. Bu alt sınıfların üst sınıflarından davranış ve nitelikleri miras almasına izin verir.

```ruby
class Vehicle
  def engine
    puts "I have an engine."
  end 
end

class Car < Vehicle
end

car = Car.new
car.engine # "I have an engine."
```

**Modüller** ise, metot ve sabitlerden oluşan koleksiyonlardır. Yeniden kullanılabilir kodu birden fazla sınıf arasında paylaşmak için kullanılabilirler. Modüller **kalıtımı desteklemez** ancak sınıfların modüllerde tanımlanan metotlara erişmesini sağlayan “modül dahil etmeye” izin verirler.

Ruby sınıfları sadece tek bir üst sınıftan doğrudan miras alabildiğinden, çoklu miras geleneksel anlamda desteklenmez. Ancak, Ruby benzer işlevselliği modüller ve `mixin` adı verilen bir özelliğin kullanımı ile elde eder.

```ruby
module Movable
  def move
    puts "I can move!"
  end
end

class Vehicle
  include Movable
end

car = Vehicle.new
car.move # "I can move!"
```

Bir modül **include** veya **extend** anahtar sözcüğü kullanılarak bir sınıfa dahil edildiğinde, bir mixin haline gelir. Bu, sınıfın modülde tanımlanan metotları ve sabitleri miras almasını sağlar. Ruby çoklu kalıtımı bu şekilde simüle eder.

Buraya kadar tamamız. Şimdi geldik size sormak istediğim o soruya. Ben şöyle bir şey yaparsam;

```ruby
module MyModule
  def my_method
    puts "I belong to MyModule"
  end
end

class MyClass
  include MyModule

  def my_method
    puts "I belong to MyClass"
  end
end

instance = MyClass.new
instance.my_method
```

Fark ettiğiniz üzere hem `MyClass` sınıfının kendinde hemde dahil edilen `MyModule` modülünde aynı isme sahip bir method var; `my_method`. Sizce Ruby hangisini yürütecektir? Neden?

Ruby’de, bir nesne üzerinde bir metot çağırmak, çalıştırılacak uygun metodu belirlemek için sistematik bir arama sürecini tetikler. Bu süreç **“Method Lookup Path”** (Metot Arama Yolu) olarak bilinir.

Ruby’de, metot arama yolu, bir nesne üzerinde çağrıldığında Ruby’nin bir metodu arama sırasıdır. Bu yol, Ruby’nin çalıştırılacak metodu nasıl bulacağını belirler ve bir dizi modül ve sınıfı geçmeyi içerir. Bu sıra şu şekildedir:

1. **Singleton Class:** Ruby ilk olarak nesnenin singleton sınıfını (eigenclass) kontrol eder. Bu sınıf nesnenin o örneği için özel olarak tanımlanmış metotları tutar.

2. **Class:** Eğer metot singleton sınıfında bulunamazsa, Ruby nesnenin sınıfına bakar.

3. **Dahil Edilen Modüller:** Eğer metot sınıfta da bulunamazsa Ruby, sınıfa dahil edilen modüllerde arama yapar. Bu modüller dahil edilme sırasının tersine göre aranır. Eğer bir sınıf birden fazla modül içeriyorsa, en son dahil edilen modül ilk olarak aranır.

4. **Superclasses:** Eğer metot sınıfta veya dahil edilen modüllerde bulunamazsa, Ruby sınıfın üst sınıflarına gider ve arama işlemini tekrarlar.

5. **Superclass’ın İçerdiği Modüller:** Her üst sınıf için Ruby ayrıca bu üst sınıfların içerdiği modülleri de kontrol eder.

6. **BasicObject:** Eğer metot hala bulunamazsa, Ruby son olarak Ruby’deki tüm sınıfların ebeveyni olan BasicObject sınıfını kontrol eder.

Ve bu da sorumuzun cevabını açıklar. Bu durumda Ruby dahil edilen modülden önce örneğin sınıfına bakacak ve çıktı `"I belong to MyClass"` olacaktır.

Özetle; Ruby’de **“Method Lookup Path”**, bir nesne üzerinde bir metot çağrıldığında Ruby’nin metodu bulmak için izlediği konumlar dizisini ifade eder. Bu mekanizma çok önemlidir çünkü Ruby, mixinler ve modüller de dahil olmak üzere güçlü bir kalıtım modeline sahip dinamik olarak yazılan bir dildir.

Bir yöntem beklendiği gibi davranmadığında, **Method Lookup Path** arama yolunu bilmek metodun nerede tanımlandığını ve geçersiz kılınıp kılınmadığını veya bir modülden dahil edilip edilmediğini belirlemeye yardımcı olabilir. Bu, hataları teşhis etmek ve düzeltmek için önemlidir.

Aynı zamanda, doğru metodun çağrılmasını sağlamaya yardımcı olur. Ruby’de, aynı isme sahip birden fazla metot farklı yerlerde bulunabilir (örneğin, farklı modüllerde, üst sınıflarda veya nesnenin singleton sınıfında). Arama yolunun anlaşılması, belirli bir bağlamda hangi metodun çağrılacağının tahmin edilmesine yardımcı olur.

<br>

---

<br>

*Evet. Benim anlatacaklarım bu kadar. Zaman ayırıp okuduğunuz için teşekkür ederim. Umarım sizlere yardımcı olabilmişimdir. Kalın sağlıcakla…*