# MuniA
//
//  FiltersSelectorView.swift
//  CollageBuilder
//
//  Created by MuniA
//

import SwiftUI

struct FiltersSelectorView: View {

    
    @Binding var filter: ColorFilter?
    let preview: CIImage
    
    private let filters = FiltersProvider.allFilters
    private let imageSize = CGSize(width: 60, height: 80)
    
    var body: some View {
        ScrollView(.horizontal) {
            LazyHStack {
                createCell(for: nil)
                ForEach(filters, id: \.id) {
                    createCell(for: $0)
                }
            }
            .padding(.bottom, 10)
        }
    }
    
    
    private func createCell(for filter: ColorFilter?) -> some View {
        Image(uiImage: filteredImage(filter))
            .overlay {
                VStack {
                    Spacer()
                    Text(filter?.name ?? "original")
                        .frame(width: imageSize.width)
                        .lineLimit(1)
                        .minimumScaleFactor(0.1)
                }
            }
            .cornerRadius(10)
            .overlay {
                if filter == self.filter {
                    RoundedRectangle(cornerRadius: 10)
                        .stroke(.green, lineWidth: 2)
                }
            }
            .onTapGesture {
                self.filter = filter
            }
    }
    
    private func filteredImage(_ filter: ColorFilter?) -> UIImage {
        let correctSize = imageSize * screenScale
        let ciImage = (filter?.apply(to: preview) ?? preview)
            .croppedAndScaled(to: correctSize)
        
        let uiImage = UIImage(
            ciImage: ciImage,
            context: SharedContext.context,
            scale: screenScale)
        
        return uiImage
    }
}

struct FiltersSelectorView_Previews: PreviewProvider {
    static var previews: some View {
        FiltersSelectorView(filter: .constant(nil),
                            preview: CIImage.red.cropped(to: .init(side: 300)))
    }
}
