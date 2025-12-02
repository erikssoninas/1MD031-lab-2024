<template>
    <header>
        <h1>Välkommen till min hamburgerrestaurang :D</h1>
    </header>

    <main>
        <section id="burgers">
            <h2>Är du sugen på att beställa hamburgare? Välj här!</h2>
            <div>
                <Burger v-for="b in menu" :burger="b" :key="b.name" :image="b.image"
                    v-on:orderedBurger="addToOrder($event)" />
            </div>
        </section>

        <section id="contact">
            <h2>Beställningsinformation</h2>
            <form>
                <p>
                    <label for="name">Namn</label><br>
                    <input type="text" id="name" v-model="costumerName" required="required"
                        placeholder="För- och efternamn">
                </p>
                <p>
                    <label for="E-mail">E-mail</label><br>
                    <input type="email" id="email" v-model="costumerEmail" required="required"
                        placeholder="E-mail address">
                </p>

                <p>
                    <label for="recipient">Betalningsmetod</label>

                    <select id="recipient" v-model="costumerPayment">
                        <option selected="selected">Swish</option>
                        <option>Kontant</option>
                        <option>Bankkort</option>
                        <option>Faktura</option>
                    </select>
                </p>
                <p>
                    <label>Kön:</label>
                <div>
                    <input type="radio" id="genderChoice1" v-model="costumerGender" value="Kvinna" />
                    <label for="contactChoice1">Kvinna</label>

                    <input type="radio" id="genderChoice2" v-model="costumerGender" value="Man" />
                    <label for="genderChoice2">Man</label>

                    <input type="radio" id="genderChoice3" v-model="costumerGender" value="Icke-binär" />
                    <label for="genderChoice3">Icke-binär</label>

                    <input type="radio" id="genderChoice4" v-model="costumerGender" value="Annan/vill inte ange" />
                    <label for="genderChoice4">Annan/vill inte ange</label>
                </div>
                </p>
            </form>

        </section>

        <div id="sendOrder">

            <div id="map" v-on:click="setLocation">
                Klicka här!
                <div id="dot" :style="{ left: (costumerLocation.x - 5) + 'px', top: (costumerLocation.y - 5) + 'px' }">
                </div>
            </div>

        </div>


        <button v-on:click="placeOrder">
            <img src="/img/bil.jfif" height="20">
            Skicka beställning!
        </button>

        <footer>
            <hr>
            Hoppas du har en fin dag och att det smakar gott!
            &copy;
        </footer>

    </main>

</template>

<script>
import Burger from '../components/Burger.vue'
import io from 'socket.io-client'
import menu from '../assets/menu.json'

const socket = io("localhost:3000");

function MenuItem(name, kCal, url, content) {
    this.name = name;
    this.kCal = kCal;
    this.image = url;
    this.content = content;
};


/*[
  new MenuItem("Hästburgare", 500, "/img/hästburgare.png", ["Hästkött", "Gluten", "Morötter"]),
  new MenuItem("Kattburgare", 350, "/img/kattburgare.png", ["Kattkött", "Tonfisk", "Laktos"]),
  new MenuItem("Hundburgare", 400, "/img/hundburgare.png", ["Hundkött", "Ben", "Skinka"])
]*/



export default {
    name: 'HomeView',
    components: {
        Burger
    },
    data: function () {
        return {
            menu,
            costumerName: "",
            costumerEmail: "",
            costumerPayment: "",
            costumerGender: "",
            orderedBurger: {},
            orderCounter: 1,
            costumerLocation: { x: 100, y: 90 }
        }
    },
    methods: {
        getOrderNumber: function () {
            return this.orderCounter++;
        },
        addToOrder: function (event) {
            this.orderedBurger[event.name] = event.amount;
        },

        setLocation: function (event) {
            var offset = {
                x: event.currentTarget.getBoundingClientRect().left,
                y: event.currentTarget.getBoundingClientRect().top
            };
            this.costumerLocation = {
                x: event.clientX - offset.x,
                y: event.clientY - offset.y
            };
        },
        placeOrder: function (event) {

            socket.emit("addOrder", {
                orderId: this.getOrderNumber(),
                details: this.costumerLocation,
                customerDetails: { name: this.costumerName, email: this.costumerEmail, payment: this.costumerPayment, gender: this.costumerGender },
                orderItems: this.orderedBurger
            });
        },
        addOrder: function (event) {
            var offset = {
                x: event.currentTarget.getBoundingClientRect().left,
                y: event.currentTarget.getBoundingClientRect().top
            };
            this.costumerLocation = {
                x: event.clientX - offset.x,
                y: event.clientY - offset.y
            };

        }
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');

body {
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    font-size: 14pt;
}

p {
    color: white;
}

h1 {
    font-family: 'Agbalumo';
    font-size: 28pt;
}

main,
header,
footer,

nav ul {
    max-width: 50rem;
    margin: 0 auto 0 auto;
}

main {
    background-color: rgb(253, 237, 218);
    border-radius: 10px;

}

header {
    padding: 5pt;
    background-size: cover;
    width: 100%;
    height: 200px;
    background-image: url("/img/restaurantImage.jpg");
    background-color: #ffffff81;
    background-blend-mode: lighten;
    display: flex;
    align-items: center;
}

header h1 {
    width: 50rem;
    margin: 0 auto;
    text-align: center;
    position: absolute;
}



nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
}

nav li {
    display: block;
    background-color: grey;
    padding: 1em;
}

#recipient {
    margin: 3px;
}

.Very-good {
    color: green;
}

.Master {
    color: green;
    font-weight: 300;
}

#burgers {
    color: beige;
    background-color: black;
    margin-top: 1em;
    padding: 10pt;
    border: 0.4em ridge brown;
    border-radius: 10px;


}

#burgers>div {
    display: grid;
    gap: 1em;
    grid-template-columns: 1fr 1fr 1fr;

}


.menu-item img {
    width: 100%;
    min-width: 0;
    height: auto;
}


button:hover {
    color: green;
    cursor: pointer;
}

button {
    margin: 5px;
}

#contact {
    border: 5pt dotted red;
    margin: 5px;
    padding: 10px;
}

#contact label {
    color: red;
}

#contact div {
    margin: 0;
}

#sendOrder {
    max-width: 50rem;
    max-height: 70vh;
    margin: 0 auto 0 auto;
    overflow: scroll;
}

#map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    position: relative;
}

#map:hover {
    cursor: crosshair;
}

#dot {
    position: absolute;
    border-radius: 50%;
    height: 10px;
    background: green;
    width: 10px;
}

footer {
    font-size: 10pt;
    margin: 5pt;
}
</style>
