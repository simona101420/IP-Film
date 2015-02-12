<?php
/**
 * Created by JetBrains PhpStorm.
 * User: Stefi
 * Date: 1/17/15
 * Time: 4:45 PM
 * To change this template use File | Settings | File Templates.
 */
include 'header.php';
include 'db1.php';

$id = htmlentities($_GET['id']);

$sql = "SELECT ime, slika, kategorija_id, detali_id, kategorija, rezija, ocena, akteri, drzava, godina, vremetraenje, sodrzina, official_trailer, sekojfilm.film_id as filmId\n"
    . " FROM\n"
    . " sekojfilm, filmovi\n"
    . " WHERE\n"
    . " filmovi.film_id = sekojfilm.film_id and\n"
    . " detali_id=" . $id;

try {
    //connect as appropriate as above
   $stmt = $db->query($sql);
    $rowCount = $stmt->rowCount();
    if($rowCount == 0){

        echo '<table width=100% border="0" style="margin-top: 80px;">';
        echo '<tr><td class="kategorija_pole">';
        echo '<div class="film_pole" style="color: white; font-size: 20px">Деталите за овој филм не се внесени.</div><div class="film_pole"></div>';
        echo '</td></tr>';
        echo '<tr><td><div class="film_pole"><div class="film_pole"></td></tr>';
        echo '<tr><td><div class="film_pole"><div class="film_pole"></td></tr>';
        echo '</table>';

    }else{
        $result = $stmt->fetchAll(PDO::FETCH_ASSOC);

        echo '<table width=100% border="0" background="images/1.jpg">';

        foreach($result as $row){

            echo'<tr align="left">' .
	'<td width="350" height="500" style="color:white"> <h1> ' . $row['ime'] . ' </h1><center><img src="' . $row['slika'] . '" width="300" height="450" border="3" alt="Parker"><br/><br/><br/>' .
	'<a href="' . $row['official_trailer'] . '" style="color:white" target = "blank"> <h2> Official Тrailer ' . $row['ime'] . ' </h2></a> </center>' .
	'</td>' .

	'<td width="500" height="450">' .
		'<p><b><font size="4" style="color:white"> Категорија: ' . $row['kategorija'] . '</font></b></p>' .
		'<p><b><font size="4" style="color:white"> Режија: ' . $row['rezija'] . ' </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Оцена: ' . round($row['ocena'], 2) . ' </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Актери: ' . $row['akteri'] . ' </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Држава: ' . $row['drzava'] . '  </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Година: ' . $row['godina'] . '  </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Времетрање: ' . $row['vremetraenje'] . ' минути </font></b></p>' .
		'<p><b><font size="4" style="color:white"> Содржина:' . $row['sodrzina'] . '  </font></b></p>' .
	'</td></tr>';
        }
         echo'<tr><td colspan="2">';

            $sql1 = "SELECT * FROM komentari,korisnici WHERE komentari.korisnik_id = korisnici.kluc AND komentari.film_id = " . $id;

            $result1 = $db->query($sql1);
            if(!$result1){
                echo '<div style="margin: 100px 50px; color: white; font-size: 20px">Коментарите не можат да се прикажат во моментов.</div>';
            }  else {
                $rowCount1 = $result1->rowCount();
            }
                if($rowCount1 == 0)
                    echo '';
        else{
            $rows = $result1->fetchAll(PDO::FETCH_ASSOC);

         echo '<table border = "1" width="100%" cellpadding="0" cellspacing="0" style="margin-top: 100px" ><thead>';
        echo'<tr style="color: white; font-size: 20px"><th colspan="2" align="center" id="th_topic">КОМЕНТАРИ</th></tr></thead><tbody>';
                foreach($rows as $one_row){
                                echo '<tr style="color:white"><td width="20%"><font size="1">' . $one_row['date'] . '</font><hr><br>' . $one_row['username'] . "</td>" ;
                                echo '<td width="80%">' . $one_row['sodrzina'] . '</td></tr>';
                            }
                            echo'</tbody></table>';
                        }
        echo '</td></tr>';


        echo '<tr><td colspan="2">';

        if($_SERVER['REQUEST_METHOD'] != 'POST')
        {  echo'<form method="post" action="detali.php?id=' . $id . '">';
            echo '</br></br><div style="color: white";>
    Коментар: </br>
    <textarea name="reply_content" rows="4" cols="60"></textarea></br>
    <input type="submit" value="Внеси" />
    </div>
    </form>';
        }
        else
        {
            $signedin = isset($_SESSION['signed_in']) ? $_SESSION['signed_in'] : '';
            if($signedin != 'true')
            {
                echo '<div style="margin: 100px 50px; color: white; font-size: 20px">Морате да бидете најавени ако сакате да оставите коментар.</div>';
            }
            else
            {
                $userid = isset($_SESSION['user_id']) ? $_SESSION['user_id'] : '';
                $sql2 = "INSERT INTO
                    komentari(sodrzina,
                          date,
                          film_id,
                          korisnik_id)
                VALUES ('" . $_POST['reply_content'] . "',
                        NOW(),
                        " . $id . ",
                        " . $userid . ")";
                $result2 = $db->query($sql2);
                if(!$result2)
                {
                    echo '<div style="margin: 100px 50px; color: white; font-size: 20px">Вашиот коментар не е зачуван, обидете се повторно.</div>';
                }
                else
                {
                     echo '<div style="color:white">Вашиот коментар е зачуван, за да го прочитате кликнете <a href="detali.php?id=' . $id . '">овде</a>.</div>';
?>

  <?php
                }
            }
        }
                echo'</td></tr>';
    }
    echo '<tr><td colspan="2"  height="250"></td></tr>';
if($rowCount1 == 0){
    echo '<tr><td colspan="2"  height="350"></td></tr>';
}
    echo '</table>';

} catch(PDOException $ex) {

    echo '<table width=100% border="0" style="margin-top: 80px;">';
    echo '<tr><td class="kategorija_pole">';
    echo '<div class="film_pole" style="color: white; font-size: 20px">Деталите за филмот не може да се прикаже во моментов. Обидете се подоцна.' . $ex->getMessage() . '</div><div class="film_pole"></div>';
    echo '</td></tr>';
    echo '<tr><td><div class="film_pole"><div class="film_pole"></td></tr>';
    echo '<tr><td><div class="film_pole"><div class="film_pole"></td></tr>';
    echo '</table>';

}        ?>



<?php
include 'footer.html';
