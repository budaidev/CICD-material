## GitLab indítása - gyakorlat
docker compose up -d
docker exec -it gitlab-gitlab-1 grep "Password:" /etc/gitlab/initial_root_password
http://localhost
Bejelentkezés: root felhasználóval
Változtassuk meg a jelszót!