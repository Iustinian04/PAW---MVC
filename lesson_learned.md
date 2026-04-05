# Lessons Learned

Proiect: Lab_06 — mini CMS (articles)

## Linku-uri utile:

- GitHub: <link-repository> https://github.com/Iustinian04/PAW---MVC
- Video demo: <link-video> https://youtu.be/mtYeYSECxwg

---

## Ce am implementat
- MVC + async/await: toate operatiile asincrone folosesc `async/await` (vezi `Controllers/ArticlesController.cs`, `Services/*`, `Repositories/*`).
- Repository + Service Layer: am separat accesul la date (Repositories) de logica de business (Services). UnitOfWork leaga repository-urile intre ele.
- Paginare si filtrare la listare (Index) cu `Previous/Next` si butoane numerotate.

---

## Raspunsuri

De ce folosim Repository Pattern?
- Ca să ascundem toată mizeria legată de baza de date într‑un singur loc. Repository‑ul se ocupă cu query‑urile și CRUD‑ul, restul aplicaţiei nu trebuie să ştie de DbContext, Include sau Where, astfel codul este mai curat.

Ce s-ar intampla daca apelam `_context` direct din controller?
- Controller-ul ar deveni greu de testat si ar creste duplicarea codului. Ar fi mai greu de intretinut si de extins.

De ce avem un Service Layer separat?
- Service layer contine regulile de business si coordoneaza operatiile intre repository-uri. Astfel controller-ul ramane responsabil doar de lucru cu HTTP si mapping intre ViewModel si model. Service-ul permite centralizarea logicii (ex: setarea `PublishedAt`).

Ce logica ar ajunge in controller fara el?
- Setari de business, validari suplimentare, transactii, combinari de date din mai multe repository-uri — practic tot ce nu tine doar de HTTP.

De ce folosim interfete (`IArticleRepository`, `IArticleService`)?
- Ca să putem schimba implementarea uşor şi să facem teste. 

Exemplu concret din cod
- Controller apeleaza `IArticleService.AddAsync(...)` (vezi `Controllers/ArticlesController.cs`).
- Service foloseste `IUnitOfWork.ArticleRepository.AddAsync(...)` si `UnitOfWork.SaveChangesAsync()` (vezi `Services/ArticleService.cs`, `Repositories/UnitOfWork.cs`).

Cum ajuta structura daca adaugam un API REST sau o aplicatie mobila?
- Refolosim `Service`-urile: API controllers sau endpoint-uri mobile pot apela aceleasi `IArticleService` fara a replica logica.


