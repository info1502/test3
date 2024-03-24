<!DOCTYPE html>
<html>
<head>
    <title>Podstrona - Profil użytkownika</title>
</head>
<body>
 <style>
        #profileImage {
            width: 150px;
            height: 150px;
        }
    </style>
    <h1>Profil użytkownika</h1>
    <div id="userProfile">
        <div>
            <img id="profileImage" src="" alt="Zdjęcie profilowe">
        </div>
        <div id="profileInfo"></div>
<div id="plakietkaElement"></div>

    </div>

    <script>
           
        // Pobierz dane zalogowanego użytkownika z localStorage
        var currentUser = localStorage.getItem('currentplayer');
        var users = JSON.parse(localStorage.getItem('users'));


        // Wyświetl profil zalogowanego użytkownika
        if (currentUser && users) {
            var userProfileDiv = document.getElementById('userProfile');
            var profileImageElement = document.getElementById('profileImage');
            var profileInfoDiv = document.getElementById('profileInfo');

            var foundUser = users.find(function(user) {
                return user.username === currentUser;
            });

            if (foundUser) {
                // Jeśli znaleziono użytkownika o nazwie 'currentUser', wyświetl jego dane na stronie
                profileImageElement.src = foundUser.profileImage;
                profileInfoDiv.innerHTML = '<p>Witaj, ' + foundUser.username + '! To jest Twój profil.</p>';

                // Wyświetl opis użytkownika, jeśli istnieje
                var storedDescription = localStorage.getItem(currentUser + '_description');
                if (storedDescription) {
                    profileInfoDiv.innerHTML += '<p>Opis: ' + storedDescription + '</p>';
                }
            } else {
                // Jeśli użytkownik o nazwie 'currentUser' nie został znaleziony, wyświetl odpowiedni komunikat
                profileInfoDiv.innerHTML = '<p>Użytkownik "' + currentUser + '" nie został znaleziony.</p>';
            }
        } else {
            // Jeśli użytkownik nie jest zalogowany lub brak danych użytkowników, wyświetl odpowiedni komunikat
            var userProfileDiv = document.getElementById('userProfile');
            userProfileDiv.innerHTML = '<p>Musisz być zalogowany, aby zobaczyć ten profil.</p>';
        }
    </script>
</body>
</html>
