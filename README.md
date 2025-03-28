# 📌 Panduan Menghancurkan Native Ads di Android

Dokumen ini menjelaskan cara menghancurkan **Native Ads** dengan benar dalam berbagai skenario UI Android agar terhindar dari **memory leak** dan **masalah performa**.

---

## 📍 **1. RecyclerView (Adapter)**
🔹 **Perlu Menghancurkan di `onViewRecycled()`**

Jika Native Ads ditampilkan dalam `RecyclerView.Adapter`, pastikan untuk menghancurkan iklan saat `ViewHolder` didaur ulang.

```java
@Override
public void onViewRecycled(@NonNull ViewHolder holder) {
    super.onViewRecycled(holder);
    if (holder.nextNativeView != null) {
        holder.nextNativeView.destroyAdmob();
        holder.nextNativeView.destroyApplovinMax();
    }
}
```

---

## 📍 **2. Activity**
🔹 **Perlu Menghancurkan di `onDestroy()`**

Jika Native Ads diinisialisasi dalam `Activity`, pastikan dihancurkan saat `onDestroy()`.

```java
@Override
protected void onDestroy() {
    super.onDestroy();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

---

## 📍 **3. Fragment**
🔹 **Perlu Menghancurkan di `onDestroyView()`**

Pastikan Native Ads dihancurkan di `onDestroyView()`, karena `Fragment` bisa mengalami re-render sebelum dihancurkan.

```java
@Override
public void onDestroyView() {
    super.onDestroyView();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

---

## 📍 **4. ViewPager2 (Onboarding / Slider)**
🔹 **Perlu Menghancurkan di `onPageSelected()` atau `onDestroyView()`**

```java
viewPager2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
    @Override
    public void onPageSelected(int position) {
        if (nextNativeView != null) {
            nextNativeView.destroyAdmob();
            nextNativeView.destroyApplovinMax();
        }
    }
});
```

Jika menggunakan `FragmentStateAdapter`:
```java
@Override
public void onDestroyView() {
    super.onDestroyView();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

---

## 📍 **5. Custom Dialog (DialogFragment / AlertDialog)**
🔹 **Perlu Menghancurkan di `onDismiss()` atau `onStop()`**

Jika menggunakan `DialogFragment`:
```java
@Override
public void onStop() {
    super.onStop();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

Jika menggunakan `AlertDialog`:
```java
dialog.setOnDismissListener(dialogInterface -> {
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
});
```

---

## 📍 **6. ViewModel**
🔹 **Perlu Menghancurkan di `onCleared()`**

Jika Native Ads diinisialisasi dalam `ViewModel`, harus dihancurkan saat `ViewModel` tidak digunakan lagi.

```java
@Override
protected void onCleared() {
    super.onCleared();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

---

## 📍 **7. Lifecycle-Aware Components**
🔹 **Perlu Menghancurkan di `ON_DESTROY` menggunakan `LifecycleObserver`**

Jika menggunakan LiveData atau komponen berbasis `LifecycleOwner`, gunakan `LifecycleObserver`.

```java
lifecycle.addObserver(new LifecycleEventObserver() {
    @Override
    public void onStateChanged(@NonNull LifecycleOwner source, @NonNull Lifecycle.Event event) {
        if (event == Lifecycle.Event.ON_DESTROY) {
            if (nextNativeView != null) {
                nextNativeView.destroyAdmob();
                nextNativeView.destroyApplovinMax();
            }
        }
    }
});
```

---

## 📍 **8. BottomSheetDialogFragment**
🔹 **Perlu Menghancurkan di `onStop()`**

```java
@Override
public void onStop() {
    super.onStop();
    if (nextNativeView != null) {
        nextNativeView.destroyAdmob();
        nextNativeView.destroyApplovinMax();
    }
}
```

---

## ✅ **Kesimpulan (Checklist Penghancuran Native Ads)**

| **Komponen UI**                | **Metode Penghancuran**                |
|---------------------------------|----------------------------------|
| **RecyclerView.Adapter**        | `onViewRecycled()` |
| **Activity**                    | `onDestroy()` |
| **Fragment**                    | `onDestroyView()` |
| **ViewPager2**                  | `onPageSelected()` / `onDestroyView()` |
| **DialogFragment**              | `onDismiss()` / `onStop()` |
| **AlertDialog**                 | `setOnDismissListener()` |
| **ViewModel**                   | `onCleared()` |
| **Lifecycle-Aware Components**  | `LifecycleObserver (ON_DESTROY)` |
| **BottomSheetDialogFragment**    | `onStop()` |

Dengan mengikuti panduan ini, **Native Ads akan dikelola dengan baik dalam berbagai skenario tanpa menyebabkan memory leak atau performa buruk!** 🚀

