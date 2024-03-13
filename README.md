# 220302031
# Davin Latif Fadilah


### Selamat Datang di CodeIgniter4
CodeIgniter4 adalah sebuah toolkit untuk membangun situs web dengan PHP. Tujuannya adalah mempercepat pengembangan proyek dengan menyediakan perpustakaan kaya untuk tugas umum dan antarmuka yang sederhana untuk mengaksesnya. Dengan CodeIgniter, Anda dapat fokus pada kreativitas Anda dengan menggunakan jumlah kode yang lebih sedikit.


## Building Response

### View Cells

CodeIgniter memungkinkan Anda membuat View Cells, yaitu fragmen tampilan kecil yang dapat digunakan berulang kali di berbagai halaman. Mereka adalah tampilan kecil yang dapat disisipkan di tempat lain, membantu memisahkan logika tampilan dan membuat kode lebih mudah dibaca dan dikontrol.
 - Simple and Controlled Cells
 - Calling a View Cell

   - Namespace Omission

   - Passing Parameters as    Key/Value String

 - Simple Cells

 - Controlled Cells

 - Creating a Controlled Cell

   - Generating Cell via Command

   - Using a Different View

   - Customize the Rendering

   - Computed Properties

   - Presentation Methods

   - Performing Setup Logic

 - Cell Caching

 ### Simple and Controlled Cells

CodeIgniter mendukung dua jenis View Cells: sederhana dan terkontrol.

View Cells sederhana dapat dibuat dari kelas dan metode apa pun yang Anda pilih dan tidak harus mengikuti aturan apa pun, kecuali bahwa ia harus mengembalikan sebuah string.

Sel Tampilan Terkontrol harus dibuat dari kelas yang memperluas kelas `Codeigniter\View\Cells\Cell` yang menyediakan kemampuan tambahan yang membuat Sel Tampilan Anda lebih fleksibel dan lebih cepat digunakan.


### Calling a View Cell

Apa pun jenis View Cell yang Anda gunakan, Anda dapat memanggilnya dari tampilan apa pun dengan menggunakan helper function `view_cell().`
Parameter pertama adalah (1) *nama kelas dan metode* (Sel Sederhana) atau (2) *nama kelas dan metode opsional* (Sel Terkendali) yang akan dipanggil, dan parameter kedua adalah larik atau string parameter yang akan diteruskan ke metode:
```bash
  // In a View.

// Simple Cell
<?= view_cell('App\Cells\MyClass::myMethod', ['param1' => 'value1', 'param2' => 'value2']) ?>

// Controlled Cell
<?= view_cell('App\Cells\MyCell', ['param1' => 'value1', 'param2' => 'value2']) ?>
```
String yang dikembalikan oleh Cell akan disisipkan ke dalam tampilan di mana fungsi `view_cell()` dipanggil.
    
### Namespace Omission

Jika Anda tidak menyertakan ruang nama lengkap untuk kelas, kelas tersebut akan diasumsikan berada di ruang nama `App\Cells.` Jadi, contoh berikut ini akan mencoba mencari kelas `MyClass` di **app/Cells/MyClass.php.** Jika tidak ditemukan di sana, semua namespace akan dipindai hingga ditemukan, mencari di dalam subdirektori Cells di setiap namespace:

```bash
  // In a View.
<?= view_cell('MyClass::myMethod', ['param1' => 'value1', 'param2' => 'value2']) ?>
```


### Passing Parameters as Key/Value String



You can also pass the parameters along as a key/value string:


```bash
  // In a View.
<?= view_cell('MyClass::myMethod', 'param1=value1, param2=value2') ?>
```

### Simple Cells

Sel Sederhana adalah kelas yang mengembalikan string dari metode yang dipilih. Contoh sel Pesan Peringatan sederhana mungkin terlihat seperti ini:


```bash
  <?php

namespace App\Cells;

class AlertMessage
{
    public function show(array $params): string
    {
        return "<div class=\"alert alert-{$params['type']}\">{$params['message']}</div>";
    }
}
```

Anda bisa menyebutnya dari dalam view seperti ini:

```bash
// In a View.
<?= view_cell('Blog::recentPosts', 'category=codeigniter, limit=5') ?>
```

```bash
<?php

// In a Cell.

namespace App\Cells;

class Blog
{
    // ...

    public function recentPosts(string $category, int $limit): string
    {
        $posts = $this->blogModel->where('category', $category)
            ->orderBy('published_on', 'desc')
            ->limit($limit)
            ->get();

        return view('recentPosts', ['posts' => $posts]);
    }
}

```

### Controlled Cells

Sel terkontrol memiliki dua tujuan utama: (1) membuat sel secepat mungkin untuk membangun sel, dan (2) memberikan logika dan fleksibilitas tambahan pada pandangan Anda, jika diperlukan.

Kelas harus memperluas `CodeIgniter\View\Cells\Cell.` Kelas tersebut harus memiliki file view dalam folder yang sama. Sesuai dengan konvensi, nama kelas harus dalam PascalCase yang diakhiri dengan Cell dan tampilan harus berupa versi snake_cased dari nama kelas, tanpa akhiran. Sebagai contoh, jika Anda memiliki kelas `MyCell,` file view haruslah `my.php.`






**Note!**
```
  Sebelum v4.3.5, file tampilan yang dihasilkan diakhiri dengan _cell.php. Meskipun v4.3.5 dan yang lebih baru akan menghasilkan file tampilan tanpa akhiran _cell, file tampilan yang sudah ada akan tetap ditemukan dan dimuat.
```

### Creating a Controlled Cell

Pada tingkat yang paling dasar, yang perlu Anda terapkan di dalam kelas adalah properti publik. Properti ini akan tersedia untuk file tampilan secara otomatis.
Menerapkan AlertMessage dari atas sebagai Controlled Cell akan terlihat seperti ini:

```bash
<?php

// app/Cells/AlertMessageCell.php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class AlertMessageCell extends Cell
{
    public $type;
    public $message;
}
```

```bash
// app/Cells/alert_message.php
<div class="alert alert-<?= esc($type, 'attr') ?>">
    <?= esc($message) ?>
</div>
```

```bash
// Called in main View:
<?= view_cell('AlertMessageCell', 'type=warning, message=Failed.') ?>
```

### Generating Cell via Command
Anda juga dapat membuat sel terkontrol melalui perintah bawaan dari CLI. Perintahnya adalah `php spark make:cell.` Perintah ini membutuhkan satu argumen, yaitu nama sel yang akan dibuat. Nama tersebut harus dalam PascalCase, dan kelas akan dibuat dalam direktori app/Cells. File tampilan juga akan dibuat di direktori **app/Cells**.

### Using a Different View 
Anda dapat menentukan nama tampilan khusus dengan mengatur properti `view` di dalam kelas. Tampilan akan ditempatkan seperti tampilan pada umumnya:

```bash
<?php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class AlertMessageCell extends Cell
{
    public $type;
    public $message;

    protected string $view = 'my/custom/view';
}
```
### Customize the Rendering

Jika Anda membutuhkan kontrol lebih besar terhadap rendering HTML, Anda dapat mengimplementasikan metode `render().` Metode ini memungkinkan Anda melakukan logika tambahan dan mengoper data tambahan pada tampilan, jika diperlukan. Metode `render()` harus mengembalikan sebuah string.

Untuk memanfaatkan fitur penuh dari Sel yang dikontrol, Anda harus menggunakan `$this->view()` sebagai pengganti fungsi helper `view()` yang normal:

```bash
<?php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class AlertMessageCell extends Cell
{
    public $type;
    public $message;

    public function render(): string
    {
        return $this->view('my/custom/view', ['extra' => 'data']);
    }
}
```

### Computed Properties 

Jika Anda perlu melakukan logika tambahan untuk satu atau beberapa properti, Anda dapat menggunakan properti yang dihitung. Hal ini membutuhkan pengaturan properti menjadi protected atau private dan mengimplementasikan metode publik yang namanya terdiri dari nama properti yang dikelilingi oleh get dan Property:

```bash
// In a View. Initialize the protected properties.
<?= view_cell('AlertMessageCell', ['type' => 'note', 'message' => 'test']) ?>

```

```bash
<?php

// app/Cells/AlertMessageCell.php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class AlertMessageCell extends Cell
{
    protected $type;
    protected $message;
    private $computed;

    public function mount(): void
    {
        $this->computed = sprintf('%s - %s', $this->type, $this->message);
    }

    public function getComputedProperty(): string
    {
        return $this->computed;
    }

    public function getTypeProperty(): string
    {
        return $this->type;
    }

    public function getMessageProperty(): string
    {
        return $this->message;
    }
}
```

```bash
// app/Cells/alert_message.php
<div>
    <p>type - <?= esc($type) ?></p>
    <p>message - <?= esc($message) ?></p>
    <p>computed: <?= esc($computed) ?></p>
</div>
```

**Important!**

```
You can’t set properties that are declared as private during cell initialization.
```

### Presentation Methods
Terkadang Anda perlu melakukan logika tambahan untuk tampilan, tetapi Anda tidak ingin meneruskannya sebagai parameter. Anda dapat mengimplementasikan metode yang akan dipanggil dari dalam tampilan sel itu sendiri. Hal ini dapat membantu keterbacaan tampilan Anda:

```bash
<?php

// app/Cells/RecentPostsCell.php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class RecentPostsCell extends Cell
{
    protected $posts;

    public function linkPost($post): string
    {
        return anchor('posts/' . $post->id, $post->title);
    }
}
```

```bash
// app/Cells/recent_posts.php
<ul>
    <?php foreach ($posts as $post): ?>
        <li><?= $this->linkPost($post) ?></li>
    <?php endforeach ?>
</ul>
```

### Performing Setup Logic
Jika Anda perlu melakukan logika tambahan sebelum tampilan di-render, Anda dapat mengimplementasikan metode `mount().` Metode ini akan dipanggil setelah kelas diinstansiasi, dan dapat digunakan untuk mengatur properti tambahan atau melakukan logika lain:

```bash
<?php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class RecentPostsCell extends Cell
{
    protected $posts;

    public function mount(): void
    {
        $this->posts = model('PostModel')->orderBy('created_at', 'DESC')->findAll(10);
    }
}
```

Anda dapat mengoper parameter tambahan ke metode `mount()` dengan mengopernya sebagai sebuah larik ke fungsi pembantu `view_cell().` Setiap parameter yang dikirim yang cocok dengan nama parameter dari metode `mount()` akan dilewatkan:

```bash
<?php

// app/Cells/RecentPostsCell.php

namespace App\Cells;

use CodeIgniter\View\Cells\Cell;

class RecentPostsCell extends Cell
{
    protected $posts;

    public function mount(?int $categoryId): void
    {
        $this->posts = model('PostModel')
            ->when(
                $categoryId,
                static fn ($query, $categoryId) => $query->where('category_id', $categoryId)
            )
            ->orderBy('created_at', 'DESC')
            ->findAll(10);
    }
}
```

```bash
// Called in main View:
<?= view_cell('RecentPostsCell', ['categoryId' => 5]) ?>
```

### Cell Caching
Anda dapat menyimpan hasil dari panggilan sel tampilan dengan mengoper jumlah detik untuk menyimpan data sebagai parameter ketiga. Ini akan menggunakan mesin cache yang saat ini dikonfigurasi:
```bash
// Cache the view for 5 minutes
<?= view_cell('App\Cells\Blog::recentPosts', 'limit=5', 300) ?>
```
Anda dapat memberikan nama khusus untuk digunakan alih-alih nama yang dibuat secara otomatis jika Anda mau, dengan memberikan nama baru sebagai parameter keempat:
```bash
// Cache the view for 5 minutes
<?= view_cell('App\Cells\Blog::recentPosts', 'limit=5', 300, 'newcacheid') ?>
```
