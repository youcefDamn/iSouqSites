// مشروع iSouqSites مع Firebase (تسجيل دخول وتخزين المواقع)

import React, { useState, useEffect } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { Input } from "@/components/ui/input"; import { Textarea } from "@/components/ui/textarea"; import { BadgeCheck, PlusCircle, Globe, LogIn, LogOut } from "lucide-react"; import { initializeApp } from "firebase/app"; import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, signOut, onAuthStateChanged, } from "firebase/auth"; import { getFirestore, collection, addDoc, getDocs, onSnapshot, query, orderBy, } from "firebase/firestore";

const firebaseConfig = { apiKey: "AIzaSyXXXXXXX", authDomain: "your-app.firebaseapp.com", projectId: "your-app", storageBucket: "your-app.appspot.com", messagingSenderId: "123456789", appId: "1:123456789:web:abcdefg", };

const app = initializeApp(firebaseConfig); const auth = getAuth(app); const db = getFirestore(app);

export default function Home() { const [form, setForm] = useState({ name: "", link: "", price: "", description: "" }); const [authForm, setAuthForm] = useState({ email: "", password: "" }); const [user, setUser] = useState(null); const [websites, setWebsites] = useState([]);

useEffect(() => { const unsubscribe = onAuthStateChanged(auth, (u) => setUser(u)); const q = query(collection(db, "websites"), orderBy("timestamp", "desc")); const unsubSites = onSnapshot(q, (snapshot) => { setWebsites(snapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() }))); }); return () => { unsubscribe(); unsubSites(); }; }, []);

const handleSiteChange = (e) => { setForm({ ...form, [e.target.name]: e.target.value }); };

const handleAddSite = async (e) => { e.preventDefault(); await addDoc(collection(db, "websites"), { ...form, user: user.email, timestamp: new Date(), }); setForm({ name: "", link: "", price: "", description: "" }); };

const register = async (e) => { e.preventDefault(); await createUserWithEmailAndPassword(auth, authForm.email, authForm.password); };

const login = async (e) => { e.preventDefault(); await signInWithEmailAndPassword(auth, authForm.email, authForm.password); };

const logout = () => signOut(auth);

return ( <div className="max-w-5xl mx-auto p-6 space-y-12"> <header className="text-center space-y-2"> <h1 className="text-4xl font-bold text-blue-600">📈 iSouqSites</h1> <p className="text-gray-600 text-lg"> منصة بيع وشراء المواقع الإلكترونية مع نظام اشتراك وحماية آمنة </p> {!user ? ( <form onSubmit={login} className="flex flex-col md:flex-row gap-3 justify-center mt-4"> <Input type="email" placeholder="البريد الإلكتروني" value={authForm.email} onChange={(e) => setAuthForm({ ...authForm, email: e.target.value })} required /> <Input type="password" placeholder="كلمة المرور" value={authForm.password} onChange={(e) => setAuthForm({ ...authForm, password: e.target.value })} required /> <div className="flex gap-2"> <Button type="submit">دخول</Button> <Button variant="outline" onClick={register}>تسجيل جديد</Button> </div> </form> ) : ( <div className="flex justify-center items-center gap-3"> <p className="text-green-600">مرحبًا، {user.email}</p> <Button onClick={logout} variant="ghost" className="text-red-600 flex items-center gap-2"> <LogOut className="w-4 h-4" /> تسجيل الخروج </Button> </div> )} </header>

{user && (
    <section className="bg-white p-6 rounded-2xl shadow-xl">
      <h2 className="text-2xl font-semibold mb-4 flex items-center gap-2">
        <PlusCircle className="text-green-600" /> إدراج موقع جديد
      </h2>
      <form onSubmit={handleAddSite} className="grid gap-4 md:grid-cols-2">
        <Input
          placeholder="اسم الموقع"
          name="name"
          value={form.name}
          onChange={handleSiteChange}
          required
        />
        <Input
          placeholder="رابط الموقع (URL)"
          name="link"
          value={form.link}
          onChange={handleSiteChange}
          required
        />
        <Input
          type="number"
          placeholder="السعر المطلوب ($)"
          name="price"
          value={form.price}
          onChange={handleSiteChange}
          required
        />
        <Textarea
          placeholder="وصف مختصر للموقع"
          name="description"
          value={form.description}
          onChange={handleSiteChange}
          className="md:col-span-2"
          required
        />
        <Button type="submit" className="md:col-span-2 w-full">نشر الموقع</Button>
      </form>
    </section>
  )}

  <section className="space-y-6">
    <h2 className="text-2xl font-semibold text-center">🌐 المواقع المتاحة</h2>
    <div className="grid md:grid-cols-2 gap-6">
      {websites.map((site) => (
        <Card key={site.id} className="hover:shadow-xl transition duration-300 rounded-2xl">
          <CardContent className="p-5 space-y-2">
            <h3 className="text-xl font-bold text-blue-800">{site.name}</h3>
            <p className="text-gray-600">{site.description}</p>
            <a
              href={site.link}
              target="_blank"
              className="text-blue-500 hover:underline flex items-center gap-1"
            >
              <Globe className="w-4 h-4" /> زيارة الموقع
            </a>
            <div className="text-green-600 font-semibold">💲 {site.price} USD</div>
            <p className="text-sm text-gray-400">📩 أُضيف بواسطة: {site.user}</p>
            <Button variant="outline">شراء الآن (مع خصم 2%)</Button>
          </CardContent>
        </Card>
      ))}
    </div>
    {websites.length === 0 && (
      <p className="text-center text-gray-500">لا توجد مواقع مضافة حتى الآن.</p>
    )}
  </section>

  <footer className="text-center text-sm text-gray-500 mt-12">
    حقوق النشر © 2025 - iSouqSites | جميع الحقوق محفوظة
  </footer>
</div>

); }

# iSouqSites
