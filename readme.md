Bird Flocking Particles

Running Instructions
g++ -o test example1.cpp InitShader.cpp -lglut -lGL -lGLU -lGLEW
./test

Demonstration Video
video_demonstrasi.mp4

Modifikasi terhadap source code awal example1.cpp
3 rule untuk flock of birds
Rule 1: Birds try to fly towards the centre of mass of neighbouring birds.

    vec4 rule1(particle particle)
    {
	    vec4 pc;

	    //Initialize pc
	    for (int j = 0; j < 3; j++) {
	        pc[j] = 0;
	    }

	    for (int i = 0; i < num_particles; i++){
	        if (isParticleSame(particles[i],particle) == 0)  { // if (particles[i] != particle) {
	            for (int j = 0; j < 3; j++) {
	                pc[j] = pc[j] + particles[i].position[j];
	            }
	        }
	    }
	    for (int j = 0; j < 3; j++) { 
	        pc[j] = pc[j] / num_particles-1;
	        pc[j] = (pc[j] - particle.position[j]);
	    }
	    return pc;
    }

Rule 2: Birds try to keep a small distance away from other objects (including other birds).

    vec4 rule2(particle particle)
    {
	    vec4 c;

	    for (int j = 0; j < 3; j++) {
	        c[j] = 0;
	    }

	    for (int i = 0; i < num_particles; i++) {
	         if (isParticleSame(particles[i],particle) == 0) { // if (particles[i] != particle) {
	            for (int j = 0; j < 3; j++) {
	                if (abs(particles[i].position[j] - particle.position[j]) < 1) {
	                   c[j] = c[j] - (particles[i].position[j] - particle.position[j]);
	                }
	            }
	        }
	    }
	    return c;
    }



Rule 3: Birds try to match velocity with near birds.

    vec4 rule3(particle particle)
    {
	    vec4 pv;

	    for (int j = 0; j < 3; j++) {
	        pv[j] = 0;
	    }

	    for (int i = 0; i < num_particles; i++) {
	         if (isParticleSame(particles[i],particle) == 0) { // if (particles[i] != particle) {
	            for (int j = 0; j < 3; j++) {
	                pv[j] = pv[j] + particles[i].velocity[j];
	            }
	        }
	    }

	    for (int j = 0; j < 3; j++) {
	        pv[j] = pv[j] / num_particles-1;
	        pv[j] = (pv[j] - particle.velocity[j]) / 8 ;
	    }

	    return pv;
    }

3 rule tersebut akan diaplikasikan untuk setiap partikel. Ditaruh di fungsi idle agar diaplikasikan terus menerus

    //Apply all 3 flock of bird rules
    for (i = 0; i < num_particles; i++) {
        for (j = 0; j < 3; j++) {
            v1 = rule1(particles[i]);
            v2 = rule2(particles[i]);
            v3 = rule3(particles[i]);
            particles[i].velocity[j] += v1[j];
            particles[i].velocity[j] += v2[j];
            particles[i].velocity[j] += v3[j];
            particles[i].position[j] += dt * particles[i].velocity[j];
        }
    }

Lalu, naikkan velocity oleh suatu angka (dalam kasus ini 1), dan ganti posisi sehingga flock of birds tidak diam di tempat saja

    //Increment velocity by 1 and change position so that flock of birds doesn't stay still
    for (i = 0; i < num_particles; i++) {
        for (j = 0; j < 3; j++) {
            particles[i].position[j] += dt * 10;
            particles[i].velocity[j] += 1;
        }
    }



