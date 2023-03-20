<script>
// @ts-nocheck
    import { io } from "socket.io-client";
    const socket = io("http://18.222.193.19:3000/");
    // http://localhost:3000/

    // http://18.222.193.19:3000/
    import axios from 'axios';
    let subject = "";
    let emailid = "";
    let password = "";
    let mailContent = "";

    let successRecipient = [];

    const getFiles = () => {
        const emailListInput = document.getElementById('emailList');
        const domainListInput = document.getElementById('domainList');
        // const attachmentInput = document.getElementById('attachment');
        return {
            emailList: emailListInput?.files[0],
            domainList: domainListInput?.files[0]
            // attachment: attachmentInput?.files[0]
        };
    }

    const handleSubmit = async() => {

        try {

            const formData = new FormData();
            formData.append('subject', subject);
            formData.append('emailid', emailid);
            formData.append('password', password);
            formData.append('mailContent', mailContent);

            const {emailList,domainList} = getFiles();
            formData.append('emailList', emailList); 
            formData.append('domainList', domainList); 
            // formData.append('attachment', attachment);
            
            
            const response = await axios.post('http://18.222.193.19:3000/upload', formData);
            console.log(response.data); 

        } catch (error) {
            console.log(error);
        }
    }

    socket.on('connect', () => {
        console.log(socket.id);
    });

    socket.on('disconnect', () => {
        console.log('Disconnected');
    });

    socket.on('mailSuccess', ({email}) => {
        console.log(`Mail sent successfully to ${email}`);
        successRecipient = [...successRecipient, email];
    })



    let pos = { x: 0, y: 0 };
	let showMenu = false;
	
	async function onRightClick(e) {
		if (showMenu) {
			showMenu = false;
			await new Promise(res => setTimeout(res, 100));
		}
		
		pos = { x: e.clientX, y: e.clientY };
		showMenu = true;
		console.log("pos:", pos)
	}
	

</script>

<section on:contextmenu|preventDefault={onRightClick}>
    <h2>Mailer</h2>
    
    <div class="container">
        <div class="form-container container-child">
            <form action="" on:submit|preventDefault={handleSubmit}>
                <h3>Form</h3>
                <div class="form-input">
                    <label for="emailid">Form Email</label>
                    <input type="text" name="emailid" id="emailid" bind:value={emailid}>
                </div>
                <div class="form-input">
                    <label for="password">Password</label>
                    <input type="text" name="password" id="password" bind:value={password}>
                </div>
                <div class="form-input">
                    <label for="emailList">Email List </label>
                    <input type="file" name="emailList" id="emailList">
                </div>
                <div class="form-input">
                    <label for="domainList">Domain List According to Email List </label>
                    <input type="file" name="DomainList" id="domainList">
                </div>
                <div class="form-input">
                    <label for="subject">Subject </label>
                    <input type="text" placeholder="`Re : Domain : ` Please Write After this  " name="subject" id="subject" bind:value={subject}>
                </div>
                <!-- <div class="form-input">
                    <label for="attachment">Attachment </label>
                    <input type="file" name="attachment" id="attachment" >
                </div> -->
                <div class="form-input">
                    <label for="mailContent">Mail Content </label>
                    <!-- <input type="text" name="mailContent" id="mailContent" bind:value={mailContent}> -->
                    <textarea type="text" name="mailContent" rows="50" cols="70" id="mailContent" bind:value={mailContent} />
                </div>
                <div >
                    <input type="submit" class="btn" value="Submit">
                </div>
            </form>
        </div>
        <div class="results-container container-child">
            <div class="results">
                <h3>Success</h3>
                <p>Total number of recipients: {successRecipient.length}</p>
                <div class="emails">
                   
                    {#each successRecipient as email, index}
                    <p>{index + 1}. {email}</p>
                {/each}
                </div>
            </div>
        </div>
    </div>
    <!-- <svelte:body on:contextmenu|preventDefault={onRightClick} /> -->
</section>

<style>
    :global(body) {
        font-family: sans-serif;
        background-color: #6a4d4d;
        color: white;
        margin: 0 auto;
        justify-content: center;
        margin-left: 180px;
    }
    :global(h2){
        text-align: center;
        font-size: 34px;
    }
    :global(h3){
        margin-bottom: 20px;
    }
   
    .container {
        width: 1100px;
        display: flex;

    }
    .container-child{
        width: 50%;
        display: flex;
        justify-content: center;
        padding: 30px 30px;
    }
    .form-input{
        display: flex;
        flex-direction: column;
        /* gap: 20px; */
        margin-bottom: 20px;
    }
    .form-input input{
        padding: 15px;
    }

    .form-input label{
        font-size: large;
    }
    .emails{
        overflow-y: scroll;
        background-color: rgb(133, 164, 164);
        height: 70vh;
        width: 30vw;
    }

    .btn {
  position: relative;
  /* bottom: 0%; */
  margin-top: 10px;
  left: 50%; 
  transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
  background-color: #f1f1f1;
  color: black;
  font-size: 16px;
  padding: 16px 30px;
  border: none;
  cursor: pointer;
  border-radius: 5px;
  text-align: center;
}

.btn:hover {
  background-color: black;
  color: white;
}
</style>

