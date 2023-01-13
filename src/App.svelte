<script lang="ts">
  let classm = "all"

  const fetchStudent = (async () => {
    let formData = new FormData();
    formData.append('level', classm);
    formData.append('Submit', 'OK');

    const response = await fetch("https://www.acsp.ac.th/random/save_re.php", {
      method: "POST",
      body: formData
    });
    const resText = await response.text();
    const parser = new DOMParser();
    const doc = parser.parseFromString(resText, "text/html");

    const studentNumber = doc.getElementsByTagName("font")[0];
    const studentName = doc.getElementsByTagName("font")[1];
    const studentClass = doc.getElementsByTagName("font")[2];
    const studentImg = doc.getElementsByTagName("img")[0];

    return {
      number: studentNumber.textContent,
      name: studentName.textContent,
      class: studentClass.textContent,
      img: "https://www.acsp.ac.th/images" + (studentImg.src.split(/images/)[1])
    }
  })

  const classOpt = [
    "P1",
    "P2",
    "P3",
    "P4",
    "P5",
    "P6",
    "M1",
    "M2",
    "M3",
    "M4",
    "M5",
    "M6",
    "all"
  ]

  let studentData = fetchStudent();
</script>

<main class="bg-gradient-to-tr from-blue-200 via-green-200 to-yellow-200">
    <div class="absolute bottom-0 left-0 p-2 opacity-30 text-sm">
        By tinarskii.com | Not associated with ACSP
    </div>
    <div class="grid grid-cols-1 h-screen w-screen place-items-center">
        <div class="flex flex-col gap-2">
            <h1 class="md:text-6xl text-3xl font-bold">
                Who will be fired?
            </h1>
            <p class="md:text-3xl text-2xl font-bold">
                ไหนใครจะโดนไล่ออก
            </p>
        </div>

        <form on:submit|preventDefault={fetchStudent()}>
            <select bind:value={classm} class="select w-full max-w-xs my-2">
                {#each classOpt as opt}
                    <option value={opt}>{opt}</option>
                {/each}
            </select>

            {#await studentData}
                <div class="flex flex-col gap-12 items-center justify-center">
                    <img src="https://www.acsp.ac.th/images/std_pic/16026.JPG" class="rounded-lg" width="200px"
                         height="240px"/>
                    <div class="flex flex-col gap-2 items-center justify-center">
                        <p class="text-2xl font-bold">
                            Loading...
                        </p>
                        <p class="text-2xl font-bold">
                            XX
                        </p>
                        <p class="text-2xl font-bold">
                            MX/X
                        </p>
                        <input class="px-4 py-2 bg-blue-500 text-white text-sm w-full rounded-lg"
                               on:click={() => studentData = fetchStudent()} value="Random" type="submit"/>
                    </div>
                </div>
            {:then student}
                <div class="flex flex-col gap-12 items-center justify-center">
                    <img src={student.img} class="rounded-lg" width="200px"
                         height="240px"/>
                    <div class="flex flex-col gap-2 items-center justify-center">
                        <p class="text-2xl font-bold">
                            {student.name}
                        </p>
                        <p class="text-2xl font-bold">
                            {student.number}
                        </p>
                        <p class="text-2xl font-bold">
                            {student.class}
                        </p>
                        <input class="px-4 py-2 bg-blue-500 text-white text-sm w-full rounded-lg"
                               on:click={() => studentData = fetchStudent()} value="Random" type="submit"/>
                    </div>
                </div>
            {:catch err}
                <p>Something went wrong</p>
            {/await}
        </form>
    </div>
</main>