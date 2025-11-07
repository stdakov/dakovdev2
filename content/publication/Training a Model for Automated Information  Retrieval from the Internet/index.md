+++
title = "Training a Model for Automated Information Retrieval from the Internet"
date = 2025-04-27
description = "This research showcases the viability of using vision-based models as a scalable alternative to traditional scraping techniques, particularly for modern, dynamic e-commerce platforms. It highlights the potential of integrating AI-driven data mining for scalable, real-world web data extraction, with implications for market analytics and automated product cataloging."
+++

## Abstract
This paper presents an end-to-end visual pipeline for automated extraction of structured product data—specifically product titles, images, and prices—from e-commerce websites. The approach leverages the You Only Look Once (YOLO) v8 object detection model to identify and localize key product elements directly from rendered webpage screenshots. A hybrid dataset generation methodology was developed, combining initial manual annotation with a scalable Java-based crawler that identifies Document Object Model (DOM) elements using Cascading Style Sheets (CSS) selectors and calculates normalized bounding boxes for YOLO-compatible training. A dataset of over 30,000 annotated screenshots from 30 unique websites was compiled to train the detection model. Evaluation on 10 previously unseen websites demonstrated robust generalization: 100% detection accuracy for product images and titles, and 60% for prices, resulting in an overall success rate of 86.7%. These results highlight the feasibility of vision-based extraction methods as a scalable alternative to traditional DOM-based scraping, particularly in contexts with diverse or dynamically generated web layouts. The paper concludes with recommendations for improving price detection accuracy through further model fine-tuning and multimodal learning techniques.
<!-- more -->

Keywords: Model, information, internet, automated retrieval
