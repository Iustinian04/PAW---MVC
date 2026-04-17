# Lessons Learned

Proiect: Lab_07 - User registration, login and role-based access control.

## Raspunsuri

1. De ce Logout este implementat ca <form method="post"> și nu ca un link <a href="/Auth/Logout">?

Ce s-ar putea întâmpla dacă logout-ul ar fi un GET accesibil printr-un link?

Raspuns: 
    Folosim un formular cu metoda POST in locul unui link (GET) ca o masura de siguranta. Daca logout-ul ar fi un link, atunci ar exista posibilitatea ca broswer-ul sa il acceseze automat in incercarea de a pre-incarca paginile pentur o navigare mai rapida.

2. De ce login-ul face doi pași în loc de unul?

var user = await _userManager.FindByEmailAsync(model.Email);  // pasul 1 var result = await _signInManager.PasswordSignInAsync(user.UserName!, ...);  // pasul 2
De ce nu există un singur apel de tipul PasswordSignInAsync(email, password, ...)? Ce diferență există între UserName și Email în Identity?

Raspuns:
    Procesul de login este impartit in doua etape. In prima faza, se cauta utilizatorul in baza de date, iar dupa folosim manager-ul de autentificare pentru ca valida parola.
    UserName este identificatorul tehnic unic folosit de sistem, iar Email este o proprietate. 

3. De ce nu este suficient să ascunzi butoanele Edit/Delete în View?

Avem deja @if (User.Identity.IsAuthenticated) în View care ascunde butoanele. De ce mai punem și [Authorize] + IsOwnerOrAdmin() în controller?

Dar invers: dacă punem doar [Authorize] în controller fără să ascundem în View, ce se întâmplă din perspectiva utilizatorului?

Raspuns:
     IsAuthenticated este folosit strict pentru estetica. Daca lasam asa, inca putem accesa aceste butoane direct din URL. Din acest motiv, este obligatoriu sa folosim atribute precum [Authorize] si verificari de logica in Controller. Fara ele, aplicatia ar fi deschisa oricui stie sa manipuleze adresa din browser.

4. Ce este middleware pipeline-ul în ASP.NET Core?

Citiți pe scurt: docs.microsoft.com — ASP.NET Core Middleware

De ce UseAuthentication() trebuie să apară înaintea UseAuthorization() în Program.cs? Ce s-ar întâmpla dacă le inversăm?

Raspuns:
    Prima trebuie sa vina inaintea celei de-a doua deoarece autorizarea se bazeaza pe identitatea stabilita la pasul anterior. Dacă le-am inversa, sistemul ar incerca sa verifice permisiunile unui utilizator care nu a fost inca identificat, tratand pe toata lumea ca fiind vizitatori anonimi si blocând accesul la resursele protejate, chiar daca utilizatorul este logat corect.

5. Ce am fi trebuit să implementăm manual dacă nu foloseam ASP.NET Core Identity?

Gândiți-vă la tot ce face Identity în spatele scenei: ce ar fi trebuit să scriem noi pentru a avea un sistem de login funcțional și sigur?

Raspuns:
    Ar trebui sa cream o intreaga infrastructura de la zero. Ar fi necesar sa scriem manual logica de securizare a parolelor folosind algoritmi de hashing, sa gestionam crearea si validarea cookie-urilor de sesiune si sa implementam toata logica de stocare in baza de date pentru utilizatori si roluri.

6. Care sunt dezavantajele folosirii ASP.NET Core Identity?

Identity este convenabil, dar vine cu compromisuri. Gândiți-vă la: ce tehnologii presupune că folosești, cât de ușor este să migrezi schema de utilizatori într-un alt sistem, ce se întâmplă dacă vrei să expui un API consumat de o aplicație mobilă sau de un frontend Angular.

Raspuns:
    Identity este rigid, pentru ca impune o structura fixa de tabele in baza de date care poate deveni dificil de modificat daca proiectul are cerinte foarte specifice. De asemenea, fiind strans legat de Entity Framework Core, migrarea catre o baza de date care nu este de tip SQL poate fi complicata.