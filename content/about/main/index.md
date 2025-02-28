---
## Configure page content in wide column
title: "" # leave blank to exclude
number_featured: 1 # pulling from mainSections in config.toml
use_featured: false # if false, use most recent by date
number_categories: 3 # set to zero to exclude
show_intro: true
intro: |
  <style>
  @keyframes wave {
    0% { transform: rotate(0deg); }
    10% { transform: rotate(14deg); }
    20% { transform: rotate(-8deg); }
    30% { transform: rotate(14deg); }
    40% { transform: rotate(-4deg); }
    50% { transform: rotate(10deg); }
    60% { transform: rotate(0deg); }
    100% { transform: rotate(0deg); }
  }

  .hand-wave {
    display: inline-block;
    animation: wave 1.5s infinite;
    transform-origin: 70% 70%;
    font-size: 72px;
  }

  .welcome {
    font-size: 72px;
    font-weight: bold;
    margin: 0;
  }

  .heading {
    margin-top: -60px;
    text-align: center;
    line-height: 1.2;
  }

  @keyframes textSlideIn {
    from {
      opacity: 0;
      transform: translateY(-20px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }

  .heading {
    animation: textSlideIn 1.2s ease-in-out;
  }

  @media (max-width: 768px) {
    .hand-wave {
      font-size: 48px;
    }

    .welcome {
      font-size: 48px;
    }

    .heading {
      margin-top: -30px;
    }
  }

  .hand-wave:hover {
    animation: wave 0.8s infinite;
    color: #007bff;
  }
  </style>

  <div class="heading">
    <span class="welcome">Welcome</span>
    <span class="hand-wave">👋</span>
  </div>

  Hello, My name is Mohammad Sadil Khan. I am currently pursuing a PhD degree at [**German Research Center for Artificial Intelligence (DFKI GmbH)**](https://av.dfki.de/members/sadil-khan/) and [**University of Kaiserslautern-Landau (RPTU)**](https://rptu.de/), under the supervision of Prof. Dr Didier Stricker. 

  I am currently working on the following domains.

  <details><summary><strong>Text-to-3D Reconstruction: </strong>Generating 3D models from detailed textual descriptions. </summary>
  <ul>
      <li><a href="https://arxiv.org/abs/2411.17945">MARVEL-40M+</a>: The largest and the most descriptive 3D Captioning Dataset. </li>
      </ul>
  </details>

  <details><summary><strong>Efficient 3D Representation:</strong>  Exploring efficient 3D representation approaches.</summary>
  </details>

  <details><summary><strong>AI-Assisted CAD designing: </strong>  Leveraging AI to accelerate computer-aided design processes.</summary>
    <ul>
      <li><a href="https://sadilkhan.github.io/text2cad-project/">TextCAD</a>: Generate Editable CAD models from text prompts (NeurIPS 2024 - Spotlight).</li>
      <li><a href="http://skazizali.com/cadsignet.github.io/">CAD-SIGNet</a>: Generate Full CAD design history from point clouds (CVPR 2024 - Highlight)..</li>
    </ul>
  </details>

  Check out my [**Projects**](/projects) and [**Publications**](/publications/) page.

  I am open to research collaboration or internship opportunity on the following domains.

  1. 3D scene or shape reconstruction.
  2. CAD design history generation using Multi-modal inputs.

  # Recent Events

  - **[25-09-2024]** Text2CAD has been accepted as **spotlight** in **NeurIPS** 2024. Check [Here](/publications/). 🎉🎉🎉

  - **[04-04-2024]** CAD-SIGNet has been accepted as **highlight** in **CVPR** 2024 *(12% of the accepted papers)*. 🎉🎉🎉

show_outro: false
outro: |

---
